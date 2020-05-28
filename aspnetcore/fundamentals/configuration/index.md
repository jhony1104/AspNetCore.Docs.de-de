---
<span data-ttu-id="c3d18-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-102">'Blazor'</span></span>
- <span data-ttu-id="c3d18-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-103">'Identity'</span></span>
- <span data-ttu-id="c3d18-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-105">'Razor'</span></span>
- <span data-ttu-id="c3d18-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="c3d18-107">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3d18-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="c3d18-108">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c3d18-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3d18-109">Die Konfiguration in ASP.NET Core erfolgt mithilfe eines oder mehrerer [Konfigurationsanbieter](#cp).</span><span class="sxs-lookup"><span data-stu-id="c3d18-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="c3d18-110">Konfigurationsanbieter lesen Konfigurationsdaten aus Schlüssel-Wert-Paaren unter Verwendung verschiedener Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="c3d18-111">Einstellungsdateien, z. B. *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c3d18-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="c3d18-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c3d18-112">Environment variables</span></span>
* <span data-ttu-id="c3d18-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c3d18-113">Azure Key Vault</span></span>
* <span data-ttu-id="c3d18-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="c3d18-114">Azure App Configuration</span></span>
* <span data-ttu-id="c3d18-115">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="c3d18-115">Command-line arguments</span></span>
* <span data-ttu-id="c3d18-116">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="c3d18-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="c3d18-117">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="c3d18-117">Directory files</span></span>
* <span data-ttu-id="c3d18-118">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="c3d18-118">In-memory .NET objects</span></span>

<span data-ttu-id="c3d18-119">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3d18-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="c3d18-120">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-120">Default configuration</span></span>

<span data-ttu-id="c3d18-121">ASP.NET Core-Web-Apps, die mit [dotnet new](/dotnet/core/tools/dotnet-new) oder Visual Studio erstellt wurden, generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c3d18-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="c3d18-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="c3d18-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="c3d18-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  Fügt eine vorhandene `IConfiguration` als Quelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3d18-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="c3d18-124">Im Fall einer Standardkonfiguration wird die [Host](#hvac)-Konfiguration hinzugefügt und als erste Quelle für die _App-_ Konfiguration festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="c3d18-125">[appsettings.json](#appsettingsjson) mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="c3d18-126">*appsettings.* `Environment` *.json* mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="c3d18-127">Beispielsweise *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="c3d18-128">[App-Geheimnisse](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="c3d18-129">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="c3d18-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="c3d18-130">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line) verwenden</span><span class="sxs-lookup"><span data-stu-id="c3d18-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="c3d18-131">Später hinzugefügte Konfigurationsanbieter überschreiben vorherige Schlüsseleinstellungen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="c3d18-132">Wenn beispielsweise `MyKey` sowohl unter *appsettings.json* als auch unter Umgebung festgelegt wird, wird der Umgebungswert verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="c3d18-133">Bei Verwendung der Standardkonfigurationsanbieter überschreibt der [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) alle anderen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="c3d18-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="c3d18-134">Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter [Standardeinstellungen für den Generator](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="c3d18-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="c3d18-135">Im folgenden Code werden die aktivierten Konfigurationsanbieter in der Reihenfolge angezeigt, in der sie hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="c3d18-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="c3d18-136">appsettings.json</span></span>

<span data-ttu-id="c3d18-137">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="c3d18-138">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-139">Der Standard-<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="c3d18-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="c3d18-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c3d18-140">*appsettings.json*</span></span>
1. <span data-ttu-id="c3d18-141">*appsettings.* `Environment` *.json*: Beispielsweise die Dateien *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="c3d18-142">Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="c3d18-143">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c3d18-144">*appsettings*.`Environment`.*json*-Werte überschreiben Schlüssel in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="c3d18-145">Standardmäßig sind dies z. B.:</span><span class="sxs-lookup"><span data-stu-id="c3d18-145">For example, by default:</span></span>

* <span data-ttu-id="c3d18-146">In der Entwicklung überschreibt die *appsettings*.***Development***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="c3d18-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="c3d18-147">In der Produktion überschreibt die *appsettings*.***Production***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="c3d18-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="c3d18-148">Dies ist beispielsweise bei der Bereitstellung der App in Azure der Fall.</span><span class="sxs-lookup"><span data-stu-id="c3d18-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="c3d18-149">Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster</span><span class="sxs-lookup"><span data-stu-id="c3d18-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="c3d18-150">Wenn die [Standard](#default)-Konfiguration verwendet wird, werden die Dateien *appsettings.json* und *appsettings.* `Environment` *.json* mit [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="c3d18-151">Änderungen an den Dateien *appsettings.json* und *appsettings.* `Environment` *.json* ***nach*** dem Start der App werden vom [JSON-Konfigurationsanbieter](#jcp) gelesen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="c3d18-152">Weitere Informationen zum Hinzufügen zusätzlicher JSON-Konfigurationsdateien finden Sie unter [JSON-Konfigurationsanbieter](#jcp) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="c3d18-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="c3d18-153">Sicherheits- und Secret-Manager</span><span class="sxs-lookup"><span data-stu-id="c3d18-153">Security and secret manager</span></span>

<span data-ttu-id="c3d18-154">Richtlinien für Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="c3d18-155">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="c3d18-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="c3d18-156">Der [Secret Manager](xref:security/app-secrets) kann zum Speichern von Geheimnissen in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="c3d18-157">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="c3d18-158">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="c3d18-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="c3d18-159">[Standardmäßig](#default) liest [Secret Manager](xref:security/app-secrets) Konfigurationseinstellungen nach dem Lesen von *appsettings.json* und *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="c3d18-160">Weitere Informationen zum Speichern von Kennwörtern oder anderen vertraulichen Daten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="c3d18-161"><xref:security/app-secrets>:  Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="c3d18-162">Der Secret Manager verwendet den [Dateikonfigurationsanbieter](#fcp), um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c3d18-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="c3d18-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="c3d18-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="c3d18-164">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="c3d18-165">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c3d18-165">Environment variables</span></span>

<span data-ttu-id="c3d18-166">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> die Konfiguration aus Schlüssel-Wert-Paaren der Umgebungsvariablen, nachdem *appsettings.json*, *appsettings.* `Environment` *.json* und [Secret Manager](xref:security/app-secrets) gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c3d18-167">Daher überschreiben aus der Umgebung gelesene Schlüsselwerte Werte, die aus *appsettings.json*, *appsettings.* `Environment` *.json* und Secret Manager gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="c3d18-168">Die folgenden `set`-Befehle:</span><span class="sxs-lookup"><span data-stu-id="c3d18-168">The following `set` commands:</span></span>

* <span data-ttu-id="c3d18-169">Legen die Umgebungsschlüssel und -werte des [vorangehenden Beispiels](#appsettingsjson) unter Windows fest.</span><span class="sxs-lookup"><span data-stu-id="c3d18-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="c3d18-170">Testen die Einstellungen bei Verwendung des [Beispieldownloads](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="c3d18-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="c3d18-171">Der `dotnet run`-Befehl muss im Projektverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="c3d18-172">Die obigen Umgebungseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-172">The preceding environment settings:</span></span>

* <span data-ttu-id="c3d18-173">Werden nur in Prozessen festgelegt, die über das Befehlsfenster gestartet werden, in dem sie festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="c3d18-174">Werden nicht von Browsern gelesen, die mit Visual Studio gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="c3d18-175">Die folgenden [setx](/windows-server/administration/windows-commands/setx)-Befehle können zum Festlegen der Umgebungsschlüssel und -werte unter Windows verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="c3d18-176">Anders als `set` werden `setx`-Einstellungen beibehalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="c3d18-177">`/M` legt die Variable in der Systemumgebung fest.</span><span class="sxs-lookup"><span data-stu-id="c3d18-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="c3d18-178">Wenn der `/M`-Schalter nicht verwendet wird, wird eine Benutzerumgebungsvariable festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="c3d18-179">Testen Sie wie folgt, ob die obigen Befehle *appsettings.json* und *appsettings.* `Environment` *.json* überschreiben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="c3d18-180">Mit Visual Studio: Beenden Sie Visual Studio, und starten Sie dann Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="c3d18-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="c3d18-181">Mit der Befehlszeilenschnittstelle: Starten Sie ein neues Befehlsfenster, und geben Sie `dotnet run` ein.</span><span class="sxs-lookup"><span data-stu-id="c3d18-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="c3d18-182">Rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> mit einer Zeichenfolge auf, um ein Präfix für Umgebungsvariablen anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="c3d18-183">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-183">In the preceding code:</span></span>

* <span data-ttu-id="c3d18-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` wird nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="c3d18-185">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="c3d18-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="c3d18-186">Umgebungsvariablen, die mit dem `MyCustomPrefix_`-Präfix festgelegt wurden, überschreiben die [Standardkonfigurationsanbieter](#default).</span><span class="sxs-lookup"><span data-stu-id="c3d18-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="c3d18-187">Dies schließt Umgebungsvariablen ohne das Präfix ein.</span><span class="sxs-lookup"><span data-stu-id="c3d18-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="c3d18-188">Das Präfix wird beim Lesen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="c3d18-189">Mit den folgenden Befehlen wird das benutzerdefinierte Präfix getestet:</span><span class="sxs-lookup"><span data-stu-id="c3d18-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="c3d18-190">Die [Standardkonfiguration](#default) lädt Umgebungsvariablen und Befehlszeilenargumente, die das Präfix `DOTNET_` und `ASPNETCORE_` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="c3d18-191">Die Präfixe `DOTNET_` und `ASPNETCORE_` werden von ASP.NET Core für die [Host- und App-Konfiguration](xref:fundamentals/host/generic-host#host-configuration), jedoch nicht für die Benutzerkonfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="c3d18-192">Weitere Informationen zur Host- und App-Konfiguration finden Sie unter [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c3d18-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="c3d18-193">Wählen Sie in [Azure App Service](https://azure.microsoft.com/services/app-service/) auf der Seite **Einstellungen > Konfiguration** die Option **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="c3d18-194">Anwendungseinstellungen von Azure App Service werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="c3d18-195">Im Ruhezustand verschlüsselt und über einen verschlüsselten Kanal übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="c3d18-196">Als Umgebungsvariablen verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c3d18-196">Exposed as environment variables.</span></span>

<span data-ttu-id="c3d18-197">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="c3d18-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="c3d18-198">Informationen zu Azure-Datenbankverbindungszeichenfolgen finden Sie unter [Präfixe für Verbindungszeichenfolgen](#constr).</span><span class="sxs-lookup"><span data-stu-id="c3d18-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="c3d18-199">Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="c3d18-199">Command-line</span></span>

<span data-ttu-id="c3d18-200">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments nach den folgenden Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="c3d18-201">Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="c3d18-202">[App-Geheimnisse (Secret Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c3d18-203">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-203">Environment variables.</span></span>

<span data-ttu-id="c3d18-204">[Standardmäßig](#default) überschreiben in der Befehlszeile festgelegte Konfigurationswerte die Konfigurationswerte, die mit allen anderen Konfigurationsanbietern festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="c3d18-205">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="c3d18-205">Command-line arguments</span></span>

<span data-ttu-id="c3d18-206">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `=` fest:</span><span class="sxs-lookup"><span data-stu-id="c3d18-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="c3d18-207">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `/` fest:</span><span class="sxs-lookup"><span data-stu-id="c3d18-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="c3d18-208">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `--` fest:</span><span class="sxs-lookup"><span data-stu-id="c3d18-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="c3d18-209">Der Schlüsselwert:</span><span class="sxs-lookup"><span data-stu-id="c3d18-209">The key value:</span></span>

* <span data-ttu-id="c3d18-210">Muss auf `=` folgen, oder der Schlüssel muss das Präfix `--` oder `/` aufweisen, wenn der Wert auf ein Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="c3d18-211">Ist nicht erforderlich, wenn `=` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="c3d18-212">Beispielsweise `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="c3d18-213">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare des Befehlszeilenarguments, die `=` verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="c3d18-214">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="c3d18-214">Switch mappings</span></span>

<span data-ttu-id="c3d18-215">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von **Schlüsselnamen**.</span><span class="sxs-lookup"><span data-stu-id="c3d18-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="c3d18-216">Stellen Sie ein Wörterbuch mit Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="c3d18-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="c3d18-217">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="c3d18-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="c3d18-218">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="c3d18-219">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="c3d18-220">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="c3d18-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="c3d18-221">Switches müssen mit `-` oder `--` beginnen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="c3d18-222">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="c3d18-223">Wenn Sie ein Switchmappingwörterbuch verwenden möchten, übergeben Sie es an den `AddCommandLine`-Abruf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="c3d18-224">Der folgende Code zeigt die Schlüsselwerte für die ersetzten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="c3d18-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-225">Führen Sie den folgenden Befehl aus, um die Schlüsselersetzung zu testen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="c3d18-226">Hinweis: Derzeit kann `=` nicht verwendet werden, um Schlüsselersatzwerte mit nur einem Bindestrich `-` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="c3d18-227">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="c3d18-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="c3d18-228">Mit dem folgenden Befehl kann die Schlüsselersetzung getestet werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="c3d18-229">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="c3d18-230">Der `AddCommandLine`-Aufruf der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c3d18-231">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der `AddCommandLine`-Methode der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="c3d18-232">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="c3d18-232">Hierarchical configuration data</span></span>

<span data-ttu-id="c3d18-233">Die Konfigurations-API liest hierarchische Konfigurationsdaten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="c3d18-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="c3d18-234">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="c3d18-235">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt einige der Konfigurationseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-236">Die bevorzugte Methode zum Lesen hierarchischer Konfigurationsdaten ist die Verwendung des Optionsmusters.</span><span class="sxs-lookup"><span data-stu-id="c3d18-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="c3d18-237">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten](#optpat) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="c3d18-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="c3d18-238">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="c3d18-239">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="c3d18-240">Konfigurationsschlüssel und -werte</span><span class="sxs-lookup"><span data-stu-id="c3d18-240">Configuration keys and values</span></span>

<span data-ttu-id="c3d18-241">Konfigurationsschlüssel:</span><span class="sxs-lookup"><span data-stu-id="c3d18-241">Configuration keys:</span></span>

* <span data-ttu-id="c3d18-242">Groß-/Kleinschreibung wird beachtet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-242">Are case-insensitive.</span></span> <span data-ttu-id="c3d18-243">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="c3d18-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="c3d18-244">Wenn ein Schlüssel und ein Wert in mehreren Konfigurationsanbietern festgelegt ist, wird der Wert des zuletzt hinzugefügten Anbieters verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="c3d18-245">Weitere Informationen finden Sie unter [Standardkonfiguration](#default).</span><span class="sxs-lookup"><span data-stu-id="c3d18-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="c3d18-246">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-246">Hierarchical keys</span></span>
  * <span data-ttu-id="c3d18-247">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="c3d18-248">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="c3d18-249">Ein doppelter Unterstrich (`__`) wird von allen Plattformen unterstützt und automatisch in einen Doppelpunkt (`:`) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="c3d18-250">In Azure Key Vault verwenden hierarchische Schlüssel `--` als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="c3d18-251">Der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) ersetzt `--` automatisch durch `:`, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="c3d18-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="c3d18-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c3d18-253">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#boa) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="c3d18-254">Konfigurationswerte:</span><span class="sxs-lookup"><span data-stu-id="c3d18-254">Configuration values:</span></span>

* <span data-ttu-id="c3d18-255">Sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-255">Are strings.</span></span>
* <span data-ttu-id="c3d18-256">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="c3d18-257">Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-257">Configuration providers</span></span>

<span data-ttu-id="c3d18-258">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="c3d18-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="c3d18-259">Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-259">Provider</span></span> | <span data-ttu-id="c3d18-260">Bereitstellung der Konfiguration über</span><span class="sxs-lookup"><span data-stu-id="c3d18-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="c3d18-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-262">'Blazor'</span></span>
- <span data-ttu-id="c3d18-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-263">'Identity'</span></span>
- <span data-ttu-id="c3d18-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-265">'Razor'</span></span>
- <span data-ttu-id="c3d18-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-268">'Blazor'</span></span>
- <span data-ttu-id="c3d18-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-269">'Identity'</span></span>
- <span data-ttu-id="c3d18-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-271">'Razor'</span></span>
- <span data-ttu-id="c3d18-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-272">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-274">'Blazor'</span></span>
- <span data-ttu-id="c3d18-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-275">'Identity'</span></span>
- <span data-ttu-id="c3d18-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-277">'Razor'</span></span>
- <span data-ttu-id="c3d18-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-280">'Blazor'</span></span>
- <span data-ttu-id="c3d18-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-281">'Identity'</span></span>
- <span data-ttu-id="c3d18-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-283">'Razor'</span></span>
- <span data-ttu-id="c3d18-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-286">'Blazor'</span></span>
- <span data-ttu-id="c3d18-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-287">'Identity'</span></span>
- <span data-ttu-id="c3d18-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-289">'Razor'</span></span>
- <span data-ttu-id="c3d18-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-292">'Blazor'</span></span>
- <span data-ttu-id="c3d18-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-293">'Identity'</span></span>
- <span data-ttu-id="c3d18-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-295">'Razor'</span></span>
- <span data-ttu-id="c3d18-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-298">'Blazor'</span></span>
- <span data-ttu-id="c3d18-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-299">'Identity'</span></span>
- <span data-ttu-id="c3d18-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-301">'Razor'</span></span>
- <span data-ttu-id="c3d18-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-304">'Blazor'</span></span>
- <span data-ttu-id="c3d18-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-305">'Identity'</span></span>
- <span data-ttu-id="c3d18-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-307">'Razor'</span></span>
- <span data-ttu-id="c3d18-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-310">'Blazor'</span></span>
- <span data-ttu-id="c3d18-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-311">'Identity'</span></span>
- <span data-ttu-id="c3d18-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-313">'Razor'</span></span>
- <span data-ttu-id="c3d18-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-316">'Blazor'</span></span>
- <span data-ttu-id="c3d18-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-317">'Identity'</span></span>
- <span data-ttu-id="c3d18-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-319">'Razor'</span></span>
- <span data-ttu-id="c3d18-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-322">'Blazor'</span></span>
- <span data-ttu-id="c3d18-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-323">'Identity'</span></span>
- <span data-ttu-id="c3d18-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-325">'Razor'</span></span>
- <span data-ttu-id="c3d18-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-328">'Blazor'</span></span>
- <span data-ttu-id="c3d18-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-329">'Identity'</span></span>
- <span data-ttu-id="c3d18-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-331">'Razor'</span></span>
- <span data-ttu-id="c3d18-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-334">'Blazor'</span></span>
- <span data-ttu-id="c3d18-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-335">'Identity'</span></span>
- <span data-ttu-id="c3d18-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-337">'Razor'</span></span>
- <span data-ttu-id="c3d18-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-340">'Blazor'</span></span>
- <span data-ttu-id="c3d18-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-341">'Identity'</span></span>
- <span data-ttu-id="c3d18-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-343">'Razor'</span></span>
- <span data-ttu-id="c3d18-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-346">'Blazor'</span></span>
- <span data-ttu-id="c3d18-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-347">'Identity'</span></span>
- <span data-ttu-id="c3d18-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-349">'Razor'</span></span>
- <span data-ttu-id="c3d18-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-352">'Blazor'</span></span>
- <span data-ttu-id="c3d18-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-353">'Identity'</span></span>
- <span data-ttu-id="c3d18-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-355">'Razor'</span></span>
- <span data-ttu-id="c3d18-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-358">'Blazor'</span></span>
- <span data-ttu-id="c3d18-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-359">'Identity'</span></span>
- <span data-ttu-id="c3d18-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-361">'Razor'</span></span>
- <span data-ttu-id="c3d18-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-362">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-363">------------------ | | [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Befehlszeilen-Konfigurationsanbieter](#clcp) | Befehlszeilenparameter | | [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle | | [Umgebungsvariablen-Konfigurationsanbieter](#evcp) | Umgebungsvariablen | | [Dateikonfigurationsanbieter](#file-configuration-provider) | INI-, JSON- und XML-Dateien| | [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien | | [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen | | [Geheimnis-Manager](xref:security/app-secrets)  | Datei im Benutzerprofilverzeichnis |</span><span class="sxs-lookup"><span data-stu-id="c3d18-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="c3d18-364">Konfigurationsquellen werden in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="c3d18-365">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="c3d18-366">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="c3d18-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="c3d18-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c3d18-367">*appsettings.json*</span></span>
1. <span data-ttu-id="c3d18-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="c3d18-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="c3d18-369">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="c3d18-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="c3d18-370">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="c3d18-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="c3d18-371">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="c3d18-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="c3d18-372">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle hinzugefügt, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="c3d18-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="c3d18-373">Die obige Sequenz von Anbietern wird in der [Standardkonfiguration](#default) verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="c3d18-374">Präfixe für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="c3d18-374">Connection string prefixes</span></span>

<span data-ttu-id="c3d18-375">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="c3d18-376">Diese Verbindungszeichenfolgen sind beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung beteiligt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="c3d18-377">Umgebungsvariablen mit den in der Tabelle aufgeführten Präfixen werden mit der [Standardkonfiguration](#default) in die App geladen bzw. wenn kein Präfix für `AddEnvironmentVariables` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="c3d18-378">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="c3d18-378">Connection string prefix</span></span> | <span data-ttu-id="c3d18-379">Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-379">Provider</span></span> |
| ---
<span data-ttu-id="c3d18-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-381">'Blazor'</span></span>
- <span data-ttu-id="c3d18-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-382">'Identity'</span></span>
- <span data-ttu-id="c3d18-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-384">'Razor'</span></span>
- <span data-ttu-id="c3d18-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-387">'Blazor'</span></span>
- <span data-ttu-id="c3d18-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-388">'Identity'</span></span>
- <span data-ttu-id="c3d18-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-390">'Razor'</span></span>
- <span data-ttu-id="c3d18-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-393">'Blazor'</span></span>
- <span data-ttu-id="c3d18-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-394">'Identity'</span></span>
- <span data-ttu-id="c3d18-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-396">'Razor'</span></span>
- <span data-ttu-id="c3d18-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-399">'Blazor'</span></span>
- <span data-ttu-id="c3d18-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-400">'Identity'</span></span>
- <span data-ttu-id="c3d18-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-402">'Razor'</span></span>
- <span data-ttu-id="c3d18-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-405">'Blazor'</span></span>
- <span data-ttu-id="c3d18-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-406">'Identity'</span></span>
- <span data-ttu-id="c3d18-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-408">'Razor'</span></span>
- <span data-ttu-id="c3d18-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-411">'Blazor'</span></span>
- <span data-ttu-id="c3d18-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-412">'Identity'</span></span>
- <span data-ttu-id="c3d18-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-414">'Razor'</span></span>
- <span data-ttu-id="c3d18-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-417">'Blazor'</span></span>
- <span data-ttu-id="c3d18-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-418">'Identity'</span></span>
- <span data-ttu-id="c3d18-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-420">'Razor'</span></span>
- <span data-ttu-id="c3d18-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-423">'Blazor'</span></span>
- <span data-ttu-id="c3d18-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-424">'Identity'</span></span>
- <span data-ttu-id="c3d18-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-426">'Razor'</span></span>
- <span data-ttu-id="c3d18-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-429">'Blazor'</span></span>
- <span data-ttu-id="c3d18-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-430">'Identity'</span></span>
- <span data-ttu-id="c3d18-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-432">'Razor'</span></span>
- <span data-ttu-id="c3d18-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-435">'Blazor'</span></span>
- <span data-ttu-id="c3d18-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-436">'Identity'</span></span>
- <span data-ttu-id="c3d18-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-438">'Razor'</span></span>
- <span data-ttu-id="c3d18-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-439">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-441">'Blazor'</span></span>
- <span data-ttu-id="c3d18-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-442">'Identity'</span></span>
- <span data-ttu-id="c3d18-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-444">'Razor'</span></span>
- <span data-ttu-id="c3d18-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-447">'Blazor'</span></span>
- <span data-ttu-id="c3d18-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-448">'Identity'</span></span>
- <span data-ttu-id="c3d18-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-450">'Razor'</span></span>
- <span data-ttu-id="c3d18-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-451">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-452">---- | | `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="c3d18-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="c3d18-453">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="c3d18-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="c3d18-454">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="c3d18-455">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="c3d18-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="c3d18-456">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-456">Environment variable key</span></span> | <span data-ttu-id="c3d18-457">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-457">Converted configuration key</span></span> | <span data-ttu-id="c3d18-458">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="c3d18-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="c3d18-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-460">'Blazor'</span></span>
- <span data-ttu-id="c3d18-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-461">'Identity'</span></span>
- <span data-ttu-id="c3d18-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-463">'Razor'</span></span>
- <span data-ttu-id="c3d18-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-466">'Blazor'</span></span>
- <span data-ttu-id="c3d18-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-467">'Identity'</span></span>
- <span data-ttu-id="c3d18-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-469">'Razor'</span></span>
- <span data-ttu-id="c3d18-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-472">'Blazor'</span></span>
- <span data-ttu-id="c3d18-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-473">'Identity'</span></span>
- <span data-ttu-id="c3d18-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-475">'Razor'</span></span>
- <span data-ttu-id="c3d18-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-478">'Blazor'</span></span>
- <span data-ttu-id="c3d18-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-479">'Identity'</span></span>
- <span data-ttu-id="c3d18-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-481">'Razor'</span></span>
- <span data-ttu-id="c3d18-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-484">'Blazor'</span></span>
- <span data-ttu-id="c3d18-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-485">'Identity'</span></span>
- <span data-ttu-id="c3d18-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-487">'Razor'</span></span>
- <span data-ttu-id="c3d18-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-490">'Blazor'</span></span>
- <span data-ttu-id="c3d18-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-491">'Identity'</span></span>
- <span data-ttu-id="c3d18-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-493">'Razor'</span></span>
- <span data-ttu-id="c3d18-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-496">'Blazor'</span></span>
- <span data-ttu-id="c3d18-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-497">'Identity'</span></span>
- <span data-ttu-id="c3d18-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-499">'Razor'</span></span>
- <span data-ttu-id="c3d18-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-502">'Blazor'</span></span>
- <span data-ttu-id="c3d18-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-503">'Identity'</span></span>
- <span data-ttu-id="c3d18-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-505">'Razor'</span></span>
- <span data-ttu-id="c3d18-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-508">'Blazor'</span></span>
- <span data-ttu-id="c3d18-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-509">'Identity'</span></span>
- <span data-ttu-id="c3d18-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-511">'Razor'</span></span>
- <span data-ttu-id="c3d18-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-514">'Blazor'</span></span>
- <span data-ttu-id="c3d18-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-515">'Identity'</span></span>
- <span data-ttu-id="c3d18-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-517">'Razor'</span></span>
- <span data-ttu-id="c3d18-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-518">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-520">'Blazor'</span></span>
- <span data-ttu-id="c3d18-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-521">'Identity'</span></span>
- <span data-ttu-id="c3d18-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-523">'Razor'</span></span>
- <span data-ttu-id="c3d18-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-526">'Blazor'</span></span>
- <span data-ttu-id="c3d18-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-527">'Identity'</span></span>
- <span data-ttu-id="c3d18-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-529">'Razor'</span></span>
- <span data-ttu-id="c3d18-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-532">'Blazor'</span></span>
- <span data-ttu-id="c3d18-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-533">'Identity'</span></span>
- <span data-ttu-id="c3d18-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-535">'Razor'</span></span>
- <span data-ttu-id="c3d18-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-538">'Blazor'</span></span>
- <span data-ttu-id="c3d18-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-539">'Identity'</span></span>
- <span data-ttu-id="c3d18-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-541">'Razor'</span></span>
- <span data-ttu-id="c3d18-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-544">'Blazor'</span></span>
- <span data-ttu-id="c3d18-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-545">'Identity'</span></span>
- <span data-ttu-id="c3d18-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-547">'Razor'</span></span>
- <span data-ttu-id="c3d18-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-550">'Blazor'</span></span>
- <span data-ttu-id="c3d18-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-551">'Identity'</span></span>
- <span data-ttu-id="c3d18-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-553">'Razor'</span></span>
- <span data-ttu-id="c3d18-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-556">'Blazor'</span></span>
- <span data-ttu-id="c3d18-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-557">'Identity'</span></span>
- <span data-ttu-id="c3d18-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-559">'Razor'</span></span>
- <span data-ttu-id="c3d18-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-562">'Blazor'</span></span>
- <span data-ttu-id="c3d18-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-563">'Identity'</span></span>
- <span data-ttu-id="c3d18-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-565">'Razor'</span></span>
- <span data-ttu-id="c3d18-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-568">'Blazor'</span></span>
- <span data-ttu-id="c3d18-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-569">'Identity'</span></span>
- <span data-ttu-id="c3d18-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-571">'Razor'</span></span>
- <span data-ttu-id="c3d18-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-574">'Blazor'</span></span>
- <span data-ttu-id="c3d18-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-575">'Identity'</span></span>
- <span data-ttu-id="c3d18-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-577">'Razor'</span></span>
- <span data-ttu-id="c3d18-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-580">'Blazor'</span></span>
- <span data-ttu-id="c3d18-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-581">'Identity'</span></span>
- <span data-ttu-id="c3d18-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-583">'Razor'</span></span>
- <span data-ttu-id="c3d18-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-584">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-586">'Blazor'</span></span>
- <span data-ttu-id="c3d18-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-587">'Identity'</span></span>
- <span data-ttu-id="c3d18-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-589">'Razor'</span></span>
- <span data-ttu-id="c3d18-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-592">'Blazor'</span></span>
- <span data-ttu-id="c3d18-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-593">'Identity'</span></span>
- <span data-ttu-id="c3d18-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-595">'Razor'</span></span>
- <span data-ttu-id="c3d18-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-598">'Blazor'</span></span>
- <span data-ttu-id="c3d18-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-599">'Identity'</span></span>
- <span data-ttu-id="c3d18-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-601">'Razor'</span></span>
- <span data-ttu-id="c3d18-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-604">'Blazor'</span></span>
- <span data-ttu-id="c3d18-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-605">'Identity'</span></span>
- <span data-ttu-id="c3d18-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-607">'Razor'</span></span>
- <span data-ttu-id="c3d18-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-610">'Blazor'</span></span>
- <span data-ttu-id="c3d18-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-611">'Identity'</span></span>
- <span data-ttu-id="c3d18-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-613">'Razor'</span></span>
- <span data-ttu-id="c3d18-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-616">'Blazor'</span></span>
- <span data-ttu-id="c3d18-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-617">'Identity'</span></span>
- <span data-ttu-id="c3d18-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-619">'Razor'</span></span>
- <span data-ttu-id="c3d18-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-622">'Blazor'</span></span>
- <span data-ttu-id="c3d18-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-623">'Identity'</span></span>
- <span data-ttu-id="c3d18-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-625">'Razor'</span></span>
- <span data-ttu-id="c3d18-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-628">'Blazor'</span></span>
- <span data-ttu-id="c3d18-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-629">'Identity'</span></span>
- <span data-ttu-id="c3d18-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-631">'Razor'</span></span>
- <span data-ttu-id="c3d18-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-634">'Blazor'</span></span>
- <span data-ttu-id="c3d18-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-635">'Identity'</span></span>
- <span data-ttu-id="c3d18-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-637">'Razor'</span></span>
- <span data-ttu-id="c3d18-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-640">'Blazor'</span></span>
- <span data-ttu-id="c3d18-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-641">'Identity'</span></span>
- <span data-ttu-id="c3d18-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-643">'Razor'</span></span>
- <span data-ttu-id="c3d18-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-646">'Blazor'</span></span>
- <span data-ttu-id="c3d18-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-647">'Identity'</span></span>
- <span data-ttu-id="c3d18-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-649">'Razor'</span></span>
- <span data-ttu-id="c3d18-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-652">'Blazor'</span></span>
- <span data-ttu-id="c3d18-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-653">'Identity'</span></span>
- <span data-ttu-id="c3d18-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-655">'Razor'</span></span>
- <span data-ttu-id="c3d18-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-658">'Blazor'</span></span>
- <span data-ttu-id="c3d18-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-659">'Identity'</span></span>
- <span data-ttu-id="c3d18-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-661">'Razor'</span></span>
- <span data-ttu-id="c3d18-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-664">'Blazor'</span></span>
- <span data-ttu-id="c3d18-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-665">'Identity'</span></span>
- <span data-ttu-id="c3d18-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-667">'Razor'</span></span>
- <span data-ttu-id="c3d18-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-670">'Blazor'</span></span>
- <span data-ttu-id="c3d18-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-671">'Identity'</span></span>
- <span data-ttu-id="c3d18-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-673">'Razor'</span></span>
- <span data-ttu-id="c3d18-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-676">'Blazor'</span></span>
- <span data-ttu-id="c3d18-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-677">'Identity'</span></span>
- <span data-ttu-id="c3d18-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-679">'Razor'</span></span>
- <span data-ttu-id="c3d18-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-682">'Blazor'</span></span>
- <span data-ttu-id="c3d18-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-683">'Identity'</span></span>
- <span data-ttu-id="c3d18-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-685">'Razor'</span></span>
- <span data-ttu-id="c3d18-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-688">'Blazor'</span></span>
- <span data-ttu-id="c3d18-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-689">'Identity'</span></span>
- <span data-ttu-id="c3d18-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-691">'Razor'</span></span>
- <span data-ttu-id="c3d18-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-694">'Blazor'</span></span>
- <span data-ttu-id="c3d18-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-695">'Identity'</span></span>
- <span data-ttu-id="c3d18-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-697">'Razor'</span></span>
- <span data-ttu-id="c3d18-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-700">'Blazor'</span></span>
- <span data-ttu-id="c3d18-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-701">'Identity'</span></span>
- <span data-ttu-id="c3d18-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-703">'Razor'</span></span>
- <span data-ttu-id="c3d18-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-706">'Blazor'</span></span>
- <span data-ttu-id="c3d18-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-707">'Identity'</span></span>
- <span data-ttu-id="c3d18-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-709">'Razor'</span></span>
- <span data-ttu-id="c3d18-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-712">'Blazor'</span></span>
- <span data-ttu-id="c3d18-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-713">'Identity'</span></span>
- <span data-ttu-id="c3d18-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-715">'Razor'</span></span>
- <span data-ttu-id="c3d18-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-718">'Blazor'</span></span>
- <span data-ttu-id="c3d18-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-719">'Identity'</span></span>
- <span data-ttu-id="c3d18-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-721">'Razor'</span></span>
- <span data-ttu-id="c3d18-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-724">'Blazor'</span></span>
- <span data-ttu-id="c3d18-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-725">'Identity'</span></span>
- <span data-ttu-id="c3d18-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-727">'Razor'</span></span>
- <span data-ttu-id="c3d18-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-730">'Blazor'</span></span>
- <span data-ttu-id="c3d18-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-731">'Identity'</span></span>
- <span data-ttu-id="c3d18-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-733">'Razor'</span></span>
- <span data-ttu-id="c3d18-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-736">'Blazor'</span></span>
- <span data-ttu-id="c3d18-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-737">'Identity'</span></span>
- <span data-ttu-id="c3d18-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-739">'Razor'</span></span>
- <span data-ttu-id="c3d18-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-742">'Blazor'</span></span>
- <span data-ttu-id="c3d18-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-743">'Identity'</span></span>
- <span data-ttu-id="c3d18-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-745">'Razor'</span></span>
- <span data-ttu-id="c3d18-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-748">'Blazor'</span></span>
- <span data-ttu-id="c3d18-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-749">'Identity'</span></span>
- <span data-ttu-id="c3d18-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-751">'Razor'</span></span>
- <span data-ttu-id="c3d18-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-754">'Blazor'</span></span>
- <span data-ttu-id="c3d18-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-755">'Identity'</span></span>
- <span data-ttu-id="c3d18-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-757">'Razor'</span></span>
- <span data-ttu-id="c3d18-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-760">'Blazor'</span></span>
- <span data-ttu-id="c3d18-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-761">'Identity'</span></span>
- <span data-ttu-id="c3d18-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-763">'Razor'</span></span>
- <span data-ttu-id="c3d18-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-766">'Blazor'</span></span>
- <span data-ttu-id="c3d18-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-767">'Identity'</span></span>
- <span data-ttu-id="c3d18-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-769">'Razor'</span></span>
- <span data-ttu-id="c3d18-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-772">'Blazor'</span></span>
- <span data-ttu-id="c3d18-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-773">'Identity'</span></span>
- <span data-ttu-id="c3d18-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-775">'Razor'</span></span>
- <span data-ttu-id="c3d18-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-778">'Blazor'</span></span>
- <span data-ttu-id="c3d18-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-779">'Identity'</span></span>
- <span data-ttu-id="c3d18-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-781">'Razor'</span></span>
- <span data-ttu-id="c3d18-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-784">'Blazor'</span></span>
- <span data-ttu-id="c3d18-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-785">'Identity'</span></span>
- <span data-ttu-id="c3d18-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-787">'Razor'</span></span>
- <span data-ttu-id="c3d18-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-790">'Blazor'</span></span>
- <span data-ttu-id="c3d18-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-791">'Identity'</span></span>
- <span data-ttu-id="c3d18-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-793">'Razor'</span></span>
- <span data-ttu-id="c3d18-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-796">'Blazor'</span></span>
- <span data-ttu-id="c3d18-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-797">'Identity'</span></span>
- <span data-ttu-id="c3d18-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-799">'Razor'</span></span>
- <span data-ttu-id="c3d18-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-802">'Blazor'</span></span>
- <span data-ttu-id="c3d18-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-803">'Identity'</span></span>
- <span data-ttu-id="c3d18-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-805">'Razor'</span></span>
- <span data-ttu-id="c3d18-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-806">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="c3d18-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-809">Wert: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-810">Wert: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-811">Wert: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="c3d18-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="c3d18-812">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-812">JSON configuration provider</span></span>

<span data-ttu-id="c3d18-813">Der <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="c3d18-814">Überladungen können Folgendes angeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-814">Overloads can specify:</span></span>

* <span data-ttu-id="c3d18-815">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="c3d18-815">Whether the file is optional.</span></span>
* <span data-ttu-id="c3d18-816">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="c3d18-817">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c3d18-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="c3d18-818">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="c3d18-818">The preceding code:</span></span>

* <span data-ttu-id="c3d18-819">Konfiguriert den JSON-Konfigurationsanbieter, sodass dieser die Datei *MyConfig.json* mit den folgenden Optionen lädt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="c3d18-820">`optional: true`: Die Datei ist optional.</span><span class="sxs-lookup"><span data-stu-id="c3d18-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="c3d18-821">`reloadOnChange: true` : Die Datei wird erneut geladen, wenn Änderungen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="c3d18-822">Liest die [Standardkonfigurationsanbieter](#default) vor der Datei *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="c3d18-823">Einstellungen in der Datei *MyConfig.json* überschreiben die Einstellung in den Standardkonfigurationsanbietern, einschließlich des [Umgebungsvariablen-Konfigurationsanbieters](#evcp) und des [Befehlszeilen-Konfigurationsanbieters](#clcp).</span><span class="sxs-lookup"><span data-stu-id="c3d18-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="c3d18-824">In der Regel möchten Sie ***nicht***, dass eine benutzerdefinierte JSON-Datei Werte überschreibt, die im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="c3d18-825">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="c3d18-826">Im obigen Code gilt für Einstellungen in den Dateien *MyConfig.json* und *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="c3d18-827">Sie überschreiben Einstellungen in den Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="c3d18-828">Sie werden durch Einstellungen im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) überschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="c3d18-829">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="c3d18-830">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="c3d18-831">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-831">File configuration provider</span></span>

<span data-ttu-id="c3d18-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="c3d18-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="c3d18-833">Die folgenden Konfigurationsanbieter leiten sich vom `FileConfigurationProvider` ab:</span><span class="sxs-lookup"><span data-stu-id="c3d18-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="c3d18-834">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="c3d18-835">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="c3d18-836">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="c3d18-837">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-837">INI configuration provider</span></span>

<span data-ttu-id="c3d18-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-839">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="c3d18-840">Im obigen Code werden Einstellungen in den Dateien *MyIniConfig.ini* und *MyIniConfig*.`Environment`.*ini* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="c3d18-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="c3d18-841">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="c3d18-842">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="c3d18-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="c3d18-843">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="c3d18-844">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="c3d18-845">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-845">XML configuration provider</span></span>

<span data-ttu-id="c3d18-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-847">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="c3d18-848">Im obigen Code werden Einstellungen in den Dateien *MyXMLFile.xml* und *MyXMLFile*.`Environment`.*xml* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="c3d18-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="c3d18-849">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="c3d18-850">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="c3d18-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="c3d18-851">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="c3d18-852">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-853">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c3d18-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="c3d18-854">Der folgende Code liest die vorherige Konfigurationsdatei und zeigt die Schlüssel und Werte an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-855">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="c3d18-856">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c3d18-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="c3d18-857">key:attribute</span></span>
* <span data-ttu-id="c3d18-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="c3d18-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="c3d18-859">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="c3d18-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="c3d18-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="c3d18-861">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="c3d18-861">The key is the file name.</span></span> <span data-ttu-id="c3d18-862">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-862">The value contains the file's contents.</span></span> <span data-ttu-id="c3d18-863">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarios verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="c3d18-864">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="c3d18-865">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="c3d18-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="c3d18-866">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="c3d18-867">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="c3d18-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="c3d18-868">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="c3d18-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="c3d18-869">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="c3d18-870">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="c3d18-871">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="c3d18-872">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-872">Memory configuration provider</span></span>

<span data-ttu-id="c3d18-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="c3d18-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="c3d18-874">Der folgende Code fügt eine Arbeitsspeichersammlung zum Konfigurationssystem hinzu:</span><span class="sxs-lookup"><span data-stu-id="c3d18-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="c3d18-875">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt die vorherigen Konfigurationseinstellungen an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-876">Im obigen Code wird `config.AddInMemoryCollection(Dict)` nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="c3d18-877">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="c3d18-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="c3d18-878">Ein weiteres Beispiel für die Verwendung von `MemoryConfigurationProvider` finden Sie unter [Binden eines Arrays](#boa).</span><span class="sxs-lookup"><span data-stu-id="c3d18-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="c3d18-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="c3d18-879">GetValue</span></span>

<span data-ttu-id="c3d18-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ:</span><span class="sxs-lookup"><span data-stu-id="c3d18-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-881">Wenn im obigen Code `NumberKey` nicht in der Konfiguration gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="c3d18-882">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="c3d18-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="c3d18-883">Beachten Sie für die nachstehenden Beispiele die folgende Datei *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="c3d18-884">Der folgende Code fügt *MySubsection.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="c3d18-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="c3d18-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="c3d18-885">GetSection</span></span>

<span data-ttu-id="c3d18-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) gibt einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel zurück.</span><span class="sxs-lookup"><span data-stu-id="c3d18-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="c3d18-887">Der folgende Code gibt Werte für `section1` zurück:</span><span class="sxs-lookup"><span data-stu-id="c3d18-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-888">Der folgende Code gibt Werte für `section2:subsection0` zurück:</span><span class="sxs-lookup"><span data-stu-id="c3d18-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-889">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="c3d18-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="c3d18-890">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="c3d18-891">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="c3d18-892">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="c3d18-893">GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="c3d18-893">GetChildren and Exists</span></span>

<span data-ttu-id="c3d18-894">Mit dem folgenden Code wird [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) abgerufen, und es werden Werte für `section2:subsection0` zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-895">Der obige Code ruft [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) auf, um zu überprüfen, ob der Abschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="c3d18-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="c3d18-896">Binden eines Arrays</span><span class="sxs-lookup"><span data-stu-id="c3d18-896">Bind an array</span></span>

<span data-ttu-id="c3d18-897">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) unterstützt das Binden von Arrays an Objekte mithilfe von Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="c3d18-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c3d18-898">Jedes Arrayformat, das ein numerisches Schlüsselsegment verfügbar macht, kann ein Array an ein [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="c3d18-899">Beachten Sie *MyArray.json* aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="c3d18-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="c3d18-900">Der folgende Code fügt *MyArray.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="c3d18-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="c3d18-901">Der folgende Code liest die Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-902">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="c3d18-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="c3d18-903">In der obigen Ausgabe hat Index 3 den Wert `value40`, der `"4": "value40",` in *MyArray.json* entspricht.</span><span class="sxs-lookup"><span data-stu-id="c3d18-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="c3d18-904">Die gebundenen Arrayindizes sind kontinuierlich und nicht an den Konfigurationsschlüsselindex gebunden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="c3d18-905">Der Konfigurationsbinder ist nicht in der Lage, NULL-Werte zu binden oder NULL-Einträge in gebundenen Objekten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="c3d18-906">Mit dem folgenden Code wird die `array:entries`-Konfiguration mit der <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode geladen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="c3d18-907">Der folgende Code liest die Konfiguration im `arrayDict` `Dictionary` und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-908">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="c3d18-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="c3d18-909">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="c3d18-910">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="c3d18-911">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="c3d18-912">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`-Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das Schlüssel-Wert-Paar von Index &num;3 liest.</span><span class="sxs-lookup"><span data-stu-id="c3d18-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="c3d18-913">Beachten Sie die folgende Datei *Value3.json* aus dem Beispieldownload:</span><span class="sxs-lookup"><span data-stu-id="c3d18-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="c3d18-914">Der folgende Code enthält die Konfiguration für *Value3.json* und das `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="c3d18-915">Der folgende Code liest die obige Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-916">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="c3d18-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="c3d18-917">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="c3d18-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="c3d18-918">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-918">Custom configuration provider</span></span>

<span data-ttu-id="c3d18-919">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="c3d18-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="c3d18-920">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="c3d18-921">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="c3d18-922">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="c3d18-923">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="c3d18-924">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="c3d18-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="c3d18-925">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="c3d18-926">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c3d18-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="c3d18-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="c3d18-928">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="c3d18-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c3d18-930">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="c3d18-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="c3d18-932">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="c3d18-933">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="c3d18-934">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="c3d18-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="c3d18-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="c3d18-936">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="c3d18-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="c3d18-938">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="c3d18-939">Zugriffskonfiguration beim Start</span><span class="sxs-lookup"><span data-stu-id="c3d18-939">Access configuration in Startup</span></span>

<span data-ttu-id="c3d18-940">Der folgende Code zeigt Konfigurationsdaten in `Startup`-Methoden an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="c3d18-941">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="c3d18-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="c3d18-942">Zugriffskonfiguration in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c3d18-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="c3d18-943">Der folgende Code zeigt Konfigurationsdaten auf einer Razor-Seite an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="c3d18-944">Im folgenden Code wird `MyOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="c3d18-945">Das folgende Markup verwendet die [`@inject`](xref:mvc/views/razor#inject) Razor Direktive, um die Optionswerte aufzulösen und anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="c3d18-946">Zugriffskonfiguration in einer MVC-Ansichtsdatei</span><span class="sxs-lookup"><span data-stu-id="c3d18-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="c3d18-947">Der folgende Code zeigt Konfigurationsdaten in einer MVC-Ansicht an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="c3d18-948">Konfigurieren von Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="c3d18-948">Configure options with a delegate</span></span>

<span data-ttu-id="c3d18-949">In einem Delegaten konfigurierte Optionen überschreiben die in den Konfigurationsanbietern festgelegten Werte.</span><span class="sxs-lookup"><span data-stu-id="c3d18-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="c3d18-950">Das Konfigurieren von Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="c3d18-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="c3d18-951">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="c3d18-952">Er verwendet einen Delegaten, um Werte für `MyOptions` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c3d18-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="c3d18-953">Im folgenden Code werden die Optionswerte angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="c3d18-954">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` in *appsettings.json* angegeben und anschließend vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="c3d18-955">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="c3d18-955">Host versus app configuration</span></span>

<span data-ttu-id="c3d18-956">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="c3d18-957">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="c3d18-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c3d18-958">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="c3d18-959">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="c3d18-960">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="c3d18-961">Standardhostkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-961">Default host configuration</span></span>

<span data-ttu-id="c3d18-962">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="c3d18-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="c3d18-963">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="c3d18-964">Umgebungsvariablen mit dem Präfix `DOTNET_` (z. B. `DOTNET_ENVIRONMENT`), die den [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) verwenden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c3d18-965">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c3d18-966">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="c3d18-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="c3d18-967">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="c3d18-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="c3d18-968">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="c3d18-969">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3d18-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="c3d18-970">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="c3d18-971">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="c3d18-972">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-972">Other configuration</span></span>

<span data-ttu-id="c3d18-973">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="c3d18-974">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="c3d18-975">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="c3d18-976">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="c3d18-977">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="c3d18-978">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="c3d18-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="c3d18-979">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="c3d18-980">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="c3d18-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="c3d18-981">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c3d18-982">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3d18-983">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c3d18-983">Additional resources</span></span>

* [<span data-ttu-id="c3d18-984">Quellcode der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3d18-985">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="c3d18-986">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="c3d18-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c3d18-987">Azure Key Vault</span></span>
* <span data-ttu-id="c3d18-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="c3d18-988">Azure App Configuration</span></span>
* <span data-ttu-id="c3d18-989">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="c3d18-989">Command-line arguments</span></span>
* <span data-ttu-id="c3d18-990">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="c3d18-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="c3d18-991">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="c3d18-991">Directory files</span></span>
* <span data-ttu-id="c3d18-992">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c3d18-992">Environment variables</span></span>
* <span data-ttu-id="c3d18-993">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="c3d18-993">In-memory .NET objects</span></span>
* <span data-ttu-id="c3d18-994">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="c3d18-994">Settings files</span></span>

<span data-ttu-id="c3d18-995">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c3d18-996">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="c3d18-997">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="c3d18-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="c3d18-998">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="c3d18-999">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c3d18-1000">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c3d18-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="c3d18-1001">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="c3d18-1001">Host versus app configuration</span></span>

<span data-ttu-id="c3d18-1002">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="c3d18-1003">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c3d18-1004">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="c3d18-1005">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="c3d18-1006">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="c3d18-1007">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-1007">Other configuration</span></span>

<span data-ttu-id="c3d18-1008">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="c3d18-1009">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="c3d18-1010">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="c3d18-1011">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="c3d18-1012">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="c3d18-1013">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="c3d18-1014">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="c3d18-1015">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-1015">Default configuration</span></span>

<span data-ttu-id="c3d18-1016">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="c3d18-1017">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="c3d18-1018">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="c3d18-1019">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="c3d18-1020">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="c3d18-1021">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c3d18-1022">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c3d18-1023">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="c3d18-1024">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="c3d18-1025">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c3d18-1026">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c3d18-1027">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="c3d18-1028">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="c3d18-1029">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="c3d18-1030">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="c3d18-1030">Security</span></span>

<span data-ttu-id="c3d18-1031">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="c3d18-1032">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="c3d18-1033">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="c3d18-1034">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="c3d18-1035">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="c3d18-1036"><xref:security/app-secrets> &ndash; Enthält Hinweise zur Verwendung von Umgebungsvariablen zum Speichern sensibler Daten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="c3d18-1037">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="c3d18-1038">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="c3d18-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="c3d18-1040">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="c3d18-1041">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="c3d18-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="c3d18-1042">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="c3d18-1043">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="c3d18-1044">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="c3d18-1045">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="c3d18-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-1046">section0:key0</span></span>
* <span data-ttu-id="c3d18-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-1047">section0:key1</span></span>
* <span data-ttu-id="c3d18-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-1048">section1:key0</span></span>
* <span data-ttu-id="c3d18-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-1049">section1:key1</span></span>

<span data-ttu-id="c3d18-1050">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="c3d18-1051">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="c3d18-1052">Konventionen</span><span class="sxs-lookup"><span data-stu-id="c3d18-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="c3d18-1053">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1053">Sources and providers</span></span>

<span data-ttu-id="c3d18-1054">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="c3d18-1055">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="c3d18-1056">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="c3d18-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c3d18-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller> von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="c3d18-1059">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="c3d18-1060">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="c3d18-1061">Tasten</span><span class="sxs-lookup"><span data-stu-id="c3d18-1061">Keys</span></span>

<span data-ttu-id="c3d18-1062">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="c3d18-1063">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="c3d18-1064">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="c3d18-1065">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="c3d18-1066">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="c3d18-1067">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="c3d18-1068">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="c3d18-1069">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="c3d18-1070">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="c3d18-1071">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="c3d18-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c3d18-1073">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="c3d18-1074">Werte</span><span class="sxs-lookup"><span data-stu-id="c3d18-1074">Values</span></span>

<span data-ttu-id="c3d18-1075">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="c3d18-1076">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1076">Values are strings.</span></span>
* <span data-ttu-id="c3d18-1077">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="c3d18-1078">Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1078">Providers</span></span>

<span data-ttu-id="c3d18-1079">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="c3d18-1080">Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1080">Provider</span></span> | <span data-ttu-id="c3d18-1081">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="c3d18-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="c3d18-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1083">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1084">'Identity'</span></span>
- <span data-ttu-id="c3d18-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1086">'Razor'</span></span>
- <span data-ttu-id="c3d18-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1089">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1090">'Identity'</span></span>
- <span data-ttu-id="c3d18-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1092">'Razor'</span></span>
- <span data-ttu-id="c3d18-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1095">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1096">'Identity'</span></span>
- <span data-ttu-id="c3d18-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1098">'Razor'</span></span>
- <span data-ttu-id="c3d18-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1101">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1102">'Identity'</span></span>
- <span data-ttu-id="c3d18-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1104">'Razor'</span></span>
- <span data-ttu-id="c3d18-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1107">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1108">'Identity'</span></span>
- <span data-ttu-id="c3d18-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1110">'Razor'</span></span>
- <span data-ttu-id="c3d18-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1113">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1114">'Identity'</span></span>
- <span data-ttu-id="c3d18-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1116">'Razor'</span></span>
- <span data-ttu-id="c3d18-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1119">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1120">'Identity'</span></span>
- <span data-ttu-id="c3d18-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1122">'Razor'</span></span>
- <span data-ttu-id="c3d18-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1125">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1126">'Identity'</span></span>
- <span data-ttu-id="c3d18-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1128">'Razor'</span></span>
- <span data-ttu-id="c3d18-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1131">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1132">'Identity'</span></span>
- <span data-ttu-id="c3d18-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1134">'Razor'</span></span>
- <span data-ttu-id="c3d18-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1137">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1138">'Identity'</span></span>
- <span data-ttu-id="c3d18-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1140">'Razor'</span></span>
- <span data-ttu-id="c3d18-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1143">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1144">'Identity'</span></span>
- <span data-ttu-id="c3d18-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1146">'Razor'</span></span>
- <span data-ttu-id="c3d18-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1149">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1150">'Identity'</span></span>
- <span data-ttu-id="c3d18-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1152">'Razor'</span></span>
- <span data-ttu-id="c3d18-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1155">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1156">'Identity'</span></span>
- <span data-ttu-id="c3d18-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1158">'Razor'</span></span>
- <span data-ttu-id="c3d18-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1161">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1162">'Identity'</span></span>
- <span data-ttu-id="c3d18-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1164">'Razor'</span></span>
- <span data-ttu-id="c3d18-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1167">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1168">'Identity'</span></span>
- <span data-ttu-id="c3d18-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1170">'Razor'</span></span>
- <span data-ttu-id="c3d18-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1173">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1174">'Identity'</span></span>
- <span data-ttu-id="c3d18-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1176">'Razor'</span></span>
- <span data-ttu-id="c3d18-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1179">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1180">'Identity'</span></span>
- <span data-ttu-id="c3d18-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1182">'Razor'</span></span>
- <span data-ttu-id="c3d18-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1184">------------------ | | [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (*Sicherheitsthemen*) | Azure Key Vault | | [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation) | Azure App Configuration | | [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter | | [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle | | [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen | | [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) | | [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien | | [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen | | [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (*Sicherheitsthemen*) | Datei im Benutzerprofilverzeichnis |</span><span class="sxs-lookup"><span data-stu-id="c3d18-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="c3d18-1185">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="c3d18-1186">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="c3d18-1187">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="c3d18-1188">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="c3d18-1189">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="c3d18-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="c3d18-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c3d18-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="c3d18-1191">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="c3d18-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="c3d18-1192">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="c3d18-1192">Environment variables</span></span>
1. <span data-ttu-id="c3d18-1193">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="c3d18-1193">Command-line arguments</span></span>

<span data-ttu-id="c3d18-1194">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="c3d18-1195">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c3d18-1196">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="c3d18-1197">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="c3d18-1198">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="c3d18-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="c3d18-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="c3d18-1200">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="c3d18-1201">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="c3d18-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="c3d18-1202">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="c3d18-1203">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="c3d18-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="c3d18-1204">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="c3d18-1205">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="c3d18-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="c3d18-1206">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c3d18-1207">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="c3d18-1208">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="c3d18-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-1210">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c3d18-1211">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="c3d18-1212">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c3d18-1213">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c3d18-1214">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="c3d18-1215">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c3d18-1216">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1216">Environment variables.</span></span>

<span data-ttu-id="c3d18-1217">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="c3d18-1218">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="c3d18-1219">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="c3d18-1220">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="c3d18-1221">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c3d18-1222">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="c3d18-1223">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="c3d18-1223">**Example**</span></span>

<span data-ttu-id="c3d18-1224">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="c3d18-1225">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="c3d18-1226">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="c3d18-1227">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c3d18-1228">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="c3d18-1229">Argumente</span><span class="sxs-lookup"><span data-stu-id="c3d18-1229">Arguments</span></span>

<span data-ttu-id="c3d18-1230">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="c3d18-1231">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="c3d18-1232">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="c3d18-1232">Key prefix</span></span>               | <span data-ttu-id="c3d18-1233">Beispiel</span><span class="sxs-lookup"><span data-stu-id="c3d18-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="c3d18-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1235">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1236">'Identity'</span></span>
- <span data-ttu-id="c3d18-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1238">'Razor'</span></span>
- <span data-ttu-id="c3d18-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1241">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1242">'Identity'</span></span>
- <span data-ttu-id="c3d18-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1244">'Razor'</span></span>
- <span data-ttu-id="c3d18-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1247">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1248">'Identity'</span></span>
- <span data-ttu-id="c3d18-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1250">'Razor'</span></span>
- <span data-ttu-id="c3d18-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1253">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1254">'Identity'</span></span>
- <span data-ttu-id="c3d18-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1256">'Razor'</span></span>
- <span data-ttu-id="c3d18-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1259">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1260">'Identity'</span></span>
- <span data-ttu-id="c3d18-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1262">'Razor'</span></span>
- <span data-ttu-id="c3d18-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1265">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1266">'Identity'</span></span>
- <span data-ttu-id="c3d18-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1268">'Razor'</span></span>
- <span data-ttu-id="c3d18-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1271">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1272">'Identity'</span></span>
- <span data-ttu-id="c3d18-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1274">'Razor'</span></span>
- <span data-ttu-id="c3d18-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1277">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1278">'Identity'</span></span>
- <span data-ttu-id="c3d18-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1280">'Razor'</span></span>
- <span data-ttu-id="c3d18-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1283">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1284">'Identity'</span></span>
- <span data-ttu-id="c3d18-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1286">'Razor'</span></span>
- <span data-ttu-id="c3d18-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1289">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1290">'Identity'</span></span>
- <span data-ttu-id="c3d18-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1292">'Razor'</span></span>
- <span data-ttu-id="c3d18-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1295">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1296">'Identity'</span></span>
- <span data-ttu-id="c3d18-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1298">'Razor'</span></span>
- <span data-ttu-id="c3d18-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1301">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1302">'Identity'</span></span>
- <span data-ttu-id="c3d18-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1304">'Razor'</span></span>
- <span data-ttu-id="c3d18-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1307">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1308">'Identity'</span></span>
- <span data-ttu-id="c3d18-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1310">'Razor'</span></span>
- <span data-ttu-id="c3d18-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1313">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1314">'Identity'</span></span>
- <span data-ttu-id="c3d18-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1316">'Razor'</span></span>
- <span data-ttu-id="c3d18-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1319">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1320">'Identity'</span></span>
- <span data-ttu-id="c3d18-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1322">'Razor'</span></span>
- <span data-ttu-id="c3d18-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1325">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1326">'Identity'</span></span>
- <span data-ttu-id="c3d18-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1328">'Razor'</span></span>
- <span data-ttu-id="c3d18-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1331">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1332">'Identity'</span></span>
- <span data-ttu-id="c3d18-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1334">'Razor'</span></span>
- <span data-ttu-id="c3d18-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1337">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1338">'Identity'</span></span>
- <span data-ttu-id="c3d18-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1340">'Razor'</span></span>
- <span data-ttu-id="c3d18-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1343">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1344">'Identity'</span></span>
- <span data-ttu-id="c3d18-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1346">'Razor'</span></span>
- <span data-ttu-id="c3d18-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1349">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1350">'Identity'</span></span>
- <span data-ttu-id="c3d18-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1352">'Razor'</span></span>
- <span data-ttu-id="c3d18-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1355">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1356">'Identity'</span></span>
- <span data-ttu-id="c3d18-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1358">'Razor'</span></span>
- <span data-ttu-id="c3d18-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1361">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1362">'Identity'</span></span>
- <span data-ttu-id="c3d18-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1364">'Razor'</span></span>
- <span data-ttu-id="c3d18-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1367">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1368">'Identity'</span></span>
- <span data-ttu-id="c3d18-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1370">'Razor'</span></span>
- <span data-ttu-id="c3d18-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1373">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1374">'Identity'</span></span>
- <span data-ttu-id="c3d18-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1376">'Razor'</span></span>
- <span data-ttu-id="c3d18-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1379">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1380">'Identity'</span></span>
- <span data-ttu-id="c3d18-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1382">'Razor'</span></span>
- <span data-ttu-id="c3d18-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1385">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1386">'Identity'</span></span>
- <span data-ttu-id="c3d18-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1388">'Razor'</span></span>
- <span data-ttu-id="c3d18-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1391">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1392">'Identity'</span></span>
- <span data-ttu-id="c3d18-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1394">'Razor'</span></span>
- <span data-ttu-id="c3d18-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1397">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1398">'Identity'</span></span>
- <span data-ttu-id="c3d18-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1400">'Razor'</span></span>
- <span data-ttu-id="c3d18-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1403">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1404">'Identity'</span></span>
- <span data-ttu-id="c3d18-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1406">'Razor'</span></span>
- <span data-ttu-id="c3d18-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1409">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1410">'Identity'</span></span>
- <span data-ttu-id="c3d18-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1412">'Razor'</span></span>
- <span data-ttu-id="c3d18-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1415">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1416">'Identity'</span></span>
- <span data-ttu-id="c3d18-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1418">'Razor'</span></span>
- <span data-ttu-id="c3d18-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1421">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1422">'Identity'</span></span>
- <span data-ttu-id="c3d18-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1424">'Razor'</span></span>
- <span data-ttu-id="c3d18-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1427">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1428">'Identity'</span></span>
- <span data-ttu-id="c3d18-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1430">'Razor'</span></span>
- <span data-ttu-id="c3d18-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1433">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1434">'Identity'</span></span>
- <span data-ttu-id="c3d18-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1436">'Razor'</span></span>
- <span data-ttu-id="c3d18-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1439">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1440">'Identity'</span></span>
- <span data-ttu-id="c3d18-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1442">'Razor'</span></span>
- <span data-ttu-id="c3d18-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1444">--------------------------- | | Kein Präfix                | `CommandLineKey1=value1`                               |
| Zwei Bindestriche (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
|Schrägstrich (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="c3d18-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="c3d18-1445">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="c3d18-1446">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="c3d18-1447">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="c3d18-1447">Switch mappings</span></span>

<span data-ttu-id="c3d18-1448">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="c3d18-1449">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="c3d18-1450">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="c3d18-1451">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="c3d18-1452">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="c3d18-1453">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="c3d18-1454">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="c3d18-1455">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="c3d18-1456">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="c3d18-1457">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="c3d18-1458">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="c3d18-1459">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="c3d18-1460">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c3d18-1461">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="c3d18-1462">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="c3d18-1463">Key</span><span class="sxs-lookup"><span data-stu-id="c3d18-1463">Key</span></span>       | <span data-ttu-id="c3d18-1464">Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-1464">Value</span></span>             |
| ---
<span data-ttu-id="c3d18-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1466">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1467">'Identity'</span></span>
- <span data-ttu-id="c3d18-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1469">'Razor'</span></span>
- <span data-ttu-id="c3d18-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1472">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1473">'Identity'</span></span>
- <span data-ttu-id="c3d18-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1475">'Razor'</span></span>
- <span data-ttu-id="c3d18-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1478">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1479">'Identity'</span></span>
- <span data-ttu-id="c3d18-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1481">'Razor'</span></span>
- <span data-ttu-id="c3d18-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1484">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1485">'Identity'</span></span>
- <span data-ttu-id="c3d18-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1487">'Razor'</span></span>
- <span data-ttu-id="c3d18-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1490">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1491">'Identity'</span></span>
- <span data-ttu-id="c3d18-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1493">'Razor'</span></span>
- <span data-ttu-id="c3d18-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1496">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1497">'Identity'</span></span>
- <span data-ttu-id="c3d18-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1499">'Razor'</span></span>
- <span data-ttu-id="c3d18-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1502">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1503">'Identity'</span></span>
- <span data-ttu-id="c3d18-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1505">'Razor'</span></span>
- <span data-ttu-id="c3d18-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1508">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1509">'Identity'</span></span>
- <span data-ttu-id="c3d18-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1511">'Razor'</span></span>
- <span data-ttu-id="c3d18-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="c3d18-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="c3d18-1514">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="c3d18-1515">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="c3d18-1516">Key</span><span class="sxs-lookup"><span data-stu-id="c3d18-1516">Key</span></span>               | <span data-ttu-id="c3d18-1517">Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-1517">Value</span></span>    |
| ---
<span data-ttu-id="c3d18-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1519">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1520">'Identity'</span></span>
- <span data-ttu-id="c3d18-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1522">'Razor'</span></span>
- <span data-ttu-id="c3d18-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1525">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1526">'Identity'</span></span>
- <span data-ttu-id="c3d18-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1528">'Razor'</span></span>
- <span data-ttu-id="c3d18-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1531">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1532">'Identity'</span></span>
- <span data-ttu-id="c3d18-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1534">'Razor'</span></span>
- <span data-ttu-id="c3d18-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1537">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1538">'Identity'</span></span>
- <span data-ttu-id="c3d18-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1540">'Razor'</span></span>
- <span data-ttu-id="c3d18-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1543">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1544">'Identity'</span></span>
- <span data-ttu-id="c3d18-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1546">'Razor'</span></span>
- <span data-ttu-id="c3d18-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1549">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1550">'Identity'</span></span>
- <span data-ttu-id="c3d18-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1552">'Razor'</span></span>
- <span data-ttu-id="c3d18-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1555">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1556">'Identity'</span></span>
- <span data-ttu-id="c3d18-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1558">'Razor'</span></span>
- <span data-ttu-id="c3d18-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1561">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1562">'Identity'</span></span>
- <span data-ttu-id="c3d18-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1564">'Razor'</span></span>
- <span data-ttu-id="c3d18-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="c3d18-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="c3d18-1567">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="c3d18-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-1569">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="c3d18-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="c3d18-1571">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="c3d18-1572">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="c3d18-1573">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c3d18-1574">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c3d18-1575">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="c3d18-1576">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="c3d18-1577">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c3d18-1578">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="c3d18-1578">Command-line arguments.</span></span>

<span data-ttu-id="c3d18-1579">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="c3d18-1580">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="c3d18-1581">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="c3d18-1582">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="c3d18-1583">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="c3d18-1583">**Example**</span></span>

<span data-ttu-id="c3d18-1584">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="c3d18-1585">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1585">Run the sample app.</span></span> <span data-ttu-id="c3d18-1586">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c3d18-1587">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="c3d18-1588">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="c3d18-1589">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="c3d18-1590">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="c3d18-1591">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="c3d18-1592">Präfixe</span><span class="sxs-lookup"><span data-stu-id="c3d18-1592">Prefixes</span></span>

<span data-ttu-id="c3d18-1593">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="c3d18-1594">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="c3d18-1595">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="c3d18-1596">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="c3d18-1597">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c3d18-1598">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="c3d18-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="c3d18-1599">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="c3d18-1600">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="c3d18-1601">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="c3d18-1601">Connection string prefix</span></span> | <span data-ttu-id="c3d18-1602">Anbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-1602">Provider</span></span> |
| ---
<span data-ttu-id="c3d18-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1604">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1605">'Identity'</span></span>
- <span data-ttu-id="c3d18-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1607">'Razor'</span></span>
- <span data-ttu-id="c3d18-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1610">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1611">'Identity'</span></span>
- <span data-ttu-id="c3d18-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1613">'Razor'</span></span>
- <span data-ttu-id="c3d18-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1616">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1617">'Identity'</span></span>
- <span data-ttu-id="c3d18-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1619">'Razor'</span></span>
- <span data-ttu-id="c3d18-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1622">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1623">'Identity'</span></span>
- <span data-ttu-id="c3d18-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1625">'Razor'</span></span>
- <span data-ttu-id="c3d18-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1628">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1629">'Identity'</span></span>
- <span data-ttu-id="c3d18-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1631">'Razor'</span></span>
- <span data-ttu-id="c3d18-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1634">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1635">'Identity'</span></span>
- <span data-ttu-id="c3d18-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1637">'Razor'</span></span>
- <span data-ttu-id="c3d18-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1640">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1641">'Identity'</span></span>
- <span data-ttu-id="c3d18-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1643">'Razor'</span></span>
- <span data-ttu-id="c3d18-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1646">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1647">'Identity'</span></span>
- <span data-ttu-id="c3d18-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1649">'Razor'</span></span>
- <span data-ttu-id="c3d18-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1652">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1653">'Identity'</span></span>
- <span data-ttu-id="c3d18-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1655">'Razor'</span></span>
- <span data-ttu-id="c3d18-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1658">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1659">'Identity'</span></span>
- <span data-ttu-id="c3d18-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1661">'Razor'</span></span>
- <span data-ttu-id="c3d18-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1664">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1665">'Identity'</span></span>
- <span data-ttu-id="c3d18-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1667">'Razor'</span></span>
- <span data-ttu-id="c3d18-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1670">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1671">'Identity'</span></span>
- <span data-ttu-id="c3d18-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1673">'Razor'</span></span>
- <span data-ttu-id="c3d18-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1675">---- | | `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="c3d18-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="c3d18-1676">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="c3d18-1677">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="c3d18-1678">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="c3d18-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="c3d18-1679">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-1679">Environment variable key</span></span> | <span data-ttu-id="c3d18-1680">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="c3d18-1680">Converted configuration key</span></span> | <span data-ttu-id="c3d18-1681">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="c3d18-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="c3d18-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1683">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1684">'Identity'</span></span>
- <span data-ttu-id="c3d18-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1686">'Razor'</span></span>
- <span data-ttu-id="c3d18-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1689">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1690">'Identity'</span></span>
- <span data-ttu-id="c3d18-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1692">'Razor'</span></span>
- <span data-ttu-id="c3d18-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1695">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1696">'Identity'</span></span>
- <span data-ttu-id="c3d18-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1698">'Razor'</span></span>
- <span data-ttu-id="c3d18-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1701">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1702">'Identity'</span></span>
- <span data-ttu-id="c3d18-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1704">'Razor'</span></span>
- <span data-ttu-id="c3d18-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1707">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1708">'Identity'</span></span>
- <span data-ttu-id="c3d18-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1710">'Razor'</span></span>
- <span data-ttu-id="c3d18-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1713">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1714">'Identity'</span></span>
- <span data-ttu-id="c3d18-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1716">'Razor'</span></span>
- <span data-ttu-id="c3d18-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1719">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1720">'Identity'</span></span>
- <span data-ttu-id="c3d18-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1722">'Razor'</span></span>
- <span data-ttu-id="c3d18-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1725">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1726">'Identity'</span></span>
- <span data-ttu-id="c3d18-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1728">'Razor'</span></span>
- <span data-ttu-id="c3d18-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1731">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1732">'Identity'</span></span>
- <span data-ttu-id="c3d18-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1734">'Razor'</span></span>
- <span data-ttu-id="c3d18-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1737">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1738">'Identity'</span></span>
- <span data-ttu-id="c3d18-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1740">'Razor'</span></span>
- <span data-ttu-id="c3d18-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1743">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1744">'Identity'</span></span>
- <span data-ttu-id="c3d18-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1746">'Razor'</span></span>
- <span data-ttu-id="c3d18-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1749">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1750">'Identity'</span></span>
- <span data-ttu-id="c3d18-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1752">'Razor'</span></span>
- <span data-ttu-id="c3d18-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1755">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1756">'Identity'</span></span>
- <span data-ttu-id="c3d18-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1758">'Razor'</span></span>
- <span data-ttu-id="c3d18-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1761">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1762">'Identity'</span></span>
- <span data-ttu-id="c3d18-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1764">'Razor'</span></span>
- <span data-ttu-id="c3d18-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1767">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1768">'Identity'</span></span>
- <span data-ttu-id="c3d18-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1770">'Razor'</span></span>
- <span data-ttu-id="c3d18-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1773">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1774">'Identity'</span></span>
- <span data-ttu-id="c3d18-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1776">'Razor'</span></span>
- <span data-ttu-id="c3d18-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1779">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1780">'Identity'</span></span>
- <span data-ttu-id="c3d18-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1782">'Razor'</span></span>
- <span data-ttu-id="c3d18-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1785">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1786">'Identity'</span></span>
- <span data-ttu-id="c3d18-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1788">'Razor'</span></span>
- <span data-ttu-id="c3d18-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1791">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1792">'Identity'</span></span>
- <span data-ttu-id="c3d18-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1794">'Razor'</span></span>
- <span data-ttu-id="c3d18-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1797">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1798">'Identity'</span></span>
- <span data-ttu-id="c3d18-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1800">'Razor'</span></span>
- <span data-ttu-id="c3d18-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1803">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1804">'Identity'</span></span>
- <span data-ttu-id="c3d18-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1806">'Razor'</span></span>
- <span data-ttu-id="c3d18-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1809">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1810">'Identity'</span></span>
- <span data-ttu-id="c3d18-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1812">'Razor'</span></span>
- <span data-ttu-id="c3d18-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1815">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1816">'Identity'</span></span>
- <span data-ttu-id="c3d18-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1818">'Razor'</span></span>
- <span data-ttu-id="c3d18-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1821">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1822">'Identity'</span></span>
- <span data-ttu-id="c3d18-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1824">'Razor'</span></span>
- <span data-ttu-id="c3d18-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1827">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1828">'Identity'</span></span>
- <span data-ttu-id="c3d18-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1830">'Razor'</span></span>
- <span data-ttu-id="c3d18-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1833">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1834">'Identity'</span></span>
- <span data-ttu-id="c3d18-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1836">'Razor'</span></span>
- <span data-ttu-id="c3d18-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1839">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1840">'Identity'</span></span>
- <span data-ttu-id="c3d18-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1842">'Razor'</span></span>
- <span data-ttu-id="c3d18-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1845">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1846">'Identity'</span></span>
- <span data-ttu-id="c3d18-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1848">'Razor'</span></span>
- <span data-ttu-id="c3d18-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1851">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1852">'Identity'</span></span>
- <span data-ttu-id="c3d18-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1854">'Razor'</span></span>
- <span data-ttu-id="c3d18-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1857">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1858">'Identity'</span></span>
- <span data-ttu-id="c3d18-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1860">'Razor'</span></span>
- <span data-ttu-id="c3d18-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1863">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1864">'Identity'</span></span>
- <span data-ttu-id="c3d18-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1866">'Razor'</span></span>
- <span data-ttu-id="c3d18-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1869">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1870">'Identity'</span></span>
- <span data-ttu-id="c3d18-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1872">'Razor'</span></span>
- <span data-ttu-id="c3d18-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1875">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1876">'Identity'</span></span>
- <span data-ttu-id="c3d18-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1878">'Razor'</span></span>
- <span data-ttu-id="c3d18-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1881">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1882">'Identity'</span></span>
- <span data-ttu-id="c3d18-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1884">'Razor'</span></span>
- <span data-ttu-id="c3d18-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1887">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1888">'Identity'</span></span>
- <span data-ttu-id="c3d18-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1890">'Razor'</span></span>
- <span data-ttu-id="c3d18-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1893">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1894">'Identity'</span></span>
- <span data-ttu-id="c3d18-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1896">'Razor'</span></span>
- <span data-ttu-id="c3d18-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1899">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1900">'Identity'</span></span>
- <span data-ttu-id="c3d18-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1902">'Razor'</span></span>
- <span data-ttu-id="c3d18-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1905">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1906">'Identity'</span></span>
- <span data-ttu-id="c3d18-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1908">'Razor'</span></span>
- <span data-ttu-id="c3d18-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1911">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1912">'Identity'</span></span>
- <span data-ttu-id="c3d18-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1914">'Razor'</span></span>
- <span data-ttu-id="c3d18-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1917">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1918">'Identity'</span></span>
- <span data-ttu-id="c3d18-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1920">'Razor'</span></span>
- <span data-ttu-id="c3d18-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1923">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1924">'Identity'</span></span>
- <span data-ttu-id="c3d18-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1926">'Razor'</span></span>
- <span data-ttu-id="c3d18-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1929">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1930">'Identity'</span></span>
- <span data-ttu-id="c3d18-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1932">'Razor'</span></span>
- <span data-ttu-id="c3d18-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1935">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1936">'Identity'</span></span>
- <span data-ttu-id="c3d18-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1938">'Razor'</span></span>
- <span data-ttu-id="c3d18-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1941">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1942">'Identity'</span></span>
- <span data-ttu-id="c3d18-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1944">'Razor'</span></span>
- <span data-ttu-id="c3d18-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1947">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1948">'Identity'</span></span>
- <span data-ttu-id="c3d18-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1950">'Razor'</span></span>
- <span data-ttu-id="c3d18-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1953">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1954">'Identity'</span></span>
- <span data-ttu-id="c3d18-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1956">'Razor'</span></span>
- <span data-ttu-id="c3d18-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1959">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1960">'Identity'</span></span>
- <span data-ttu-id="c3d18-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1962">'Razor'</span></span>
- <span data-ttu-id="c3d18-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1965">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1966">'Identity'</span></span>
- <span data-ttu-id="c3d18-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1968">'Razor'</span></span>
- <span data-ttu-id="c3d18-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1971">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1972">'Identity'</span></span>
- <span data-ttu-id="c3d18-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1974">'Razor'</span></span>
- <span data-ttu-id="c3d18-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1977">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1978">'Identity'</span></span>
- <span data-ttu-id="c3d18-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1980">'Razor'</span></span>
- <span data-ttu-id="c3d18-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1983">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1984">'Identity'</span></span>
- <span data-ttu-id="c3d18-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1986">'Razor'</span></span>
- <span data-ttu-id="c3d18-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1989">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1990">'Identity'</span></span>
- <span data-ttu-id="c3d18-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1992">'Razor'</span></span>
- <span data-ttu-id="c3d18-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1995">'Blazor'</span></span>
- <span data-ttu-id="c3d18-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1996">'Identity'</span></span>
- <span data-ttu-id="c3d18-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-1998">'Razor'</span></span>
- <span data-ttu-id="c3d18-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2001">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2002">'Identity'</span></span>
- <span data-ttu-id="c3d18-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2004">'Razor'</span></span>
- <span data-ttu-id="c3d18-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2007">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2008">'Identity'</span></span>
- <span data-ttu-id="c3d18-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2010">'Razor'</span></span>
- <span data-ttu-id="c3d18-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2013">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2014">'Identity'</span></span>
- <span data-ttu-id="c3d18-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2016">'Razor'</span></span>
- <span data-ttu-id="c3d18-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2019">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2020">'Identity'</span></span>
- <span data-ttu-id="c3d18-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2022">'Razor'</span></span>
- <span data-ttu-id="c3d18-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2025">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2026">'Identity'</span></span>
- <span data-ttu-id="c3d18-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2028">'Razor'</span></span>
- <span data-ttu-id="c3d18-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="c3d18-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-2032">Wert: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-2033">Wert: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="c3d18-2034">Wert: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="c3d18-2035">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="c3d18-2035">**Example**</span></span>

<span data-ttu-id="c3d18-2036">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="c3d18-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="c3d18-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="c3d18-2038">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="c3d18-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="c3d18-2039">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="c3d18-2040">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2040">File Configuration Provider</span></span>

<span data-ttu-id="c3d18-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="c3d18-2042">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="c3d18-2043">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="c3d18-2044">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="c3d18-2045">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="c3d18-2046">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2046">INI Configuration Provider</span></span>

<span data-ttu-id="c3d18-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-2048">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c3d18-2049">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="c3d18-2050">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="c3d18-2051">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="c3d18-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="c3d18-2052">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c3d18-2053">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c3d18-2054">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c3d18-2055">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="c3d18-2056">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c3d18-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2057">section0:key0</span></span>
* <span data-ttu-id="c3d18-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2058">section0:key1</span></span>
* <span data-ttu-id="c3d18-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2059">section1:subsection:key</span></span>
* <span data-ttu-id="c3d18-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="c3d18-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="c3d18-2062">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="c3d18-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="c3d18-2064">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c3d18-2065">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="c3d18-2066">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="c3d18-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="c3d18-2067">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c3d18-2068">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c3d18-2069">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c3d18-2070">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="c3d18-2071">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="c3d18-2072">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="c3d18-2073">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="c3d18-2074">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="c3d18-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c3d18-2075">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c3d18-2076">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2076">Environment variables.</span></span>
* <span data-ttu-id="c3d18-2077">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c3d18-2078">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="c3d18-2078">Command-line arguments.</span></span>

<span data-ttu-id="c3d18-2079">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="c3d18-2080">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="c3d18-2081">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c3d18-2082">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="c3d18-2082">**Example**</span></span>

<span data-ttu-id="c3d18-2083">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="c3d18-2084">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="c3d18-2085">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="c3d18-2086">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="c3d18-2087">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2087">Run the sample app.</span></span> <span data-ttu-id="c3d18-2088">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c3d18-2089">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="c3d18-2090">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="c3d18-2091">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="c3d18-2092">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="c3d18-2093">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="c3d18-2094">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="c3d18-2095">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="c3d18-2096">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="c3d18-2097">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2097">XML Configuration Provider</span></span>

<span data-ttu-id="c3d18-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="c3d18-2099">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c3d18-2100">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="c3d18-2101">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="c3d18-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="c3d18-2102">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c3d18-2103">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="c3d18-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c3d18-2104">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="c3d18-2105">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="c3d18-2106">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c3d18-2107">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="c3d18-2108">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c3d18-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2109">section0:key0</span></span>
* <span data-ttu-id="c3d18-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2110">section0:key1</span></span>
* <span data-ttu-id="c3d18-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2111">section1:key0</span></span>
* <span data-ttu-id="c3d18-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2112">section1:key1</span></span>

<span data-ttu-id="c3d18-2113">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="c3d18-2114">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c3d18-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="c3d18-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="c3d18-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="c3d18-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2118">section:section1:key:key1</span></span>

<span data-ttu-id="c3d18-2119">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="c3d18-2120">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c3d18-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="c3d18-2121">key:attribute</span></span>
* <span data-ttu-id="c3d18-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="c3d18-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="c3d18-2123">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="c3d18-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="c3d18-2125">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2125">The key is the file name.</span></span> <span data-ttu-id="c3d18-2126">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2126">The value contains the file's contents.</span></span> <span data-ttu-id="c3d18-2127">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="c3d18-2128">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="c3d18-2129">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="c3d18-2130">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="c3d18-2131">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="c3d18-2132">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="c3d18-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="c3d18-2133">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="c3d18-2134">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="c3d18-2135">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="c3d18-2136">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="c3d18-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="c3d18-2138">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c3d18-2139">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="c3d18-2140">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="c3d18-2141">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="c3d18-2142">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="c3d18-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="c3d18-2143">GetValue</span></span>

<span data-ttu-id="c3d18-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Sammlungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="c3d18-2145">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="c3d18-2146">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2146">The following example:</span></span>

* <span data-ttu-id="c3d18-2147">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="c3d18-2148">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="c3d18-2149">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="c3d18-2150">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="c3d18-2151">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="c3d18-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="c3d18-2152">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="c3d18-2153">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="c3d18-2154">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="c3d18-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2155">section0:key0</span></span>
* <span data-ttu-id="c3d18-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2156">section0:key1</span></span>
* <span data-ttu-id="c3d18-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2157">section1:key0</span></span>
* <span data-ttu-id="c3d18-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2158">section1:key1</span></span>
* <span data-ttu-id="c3d18-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="c3d18-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="c3d18-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="c3d18-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="c3d18-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="c3d18-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="c3d18-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="c3d18-2163">GetSection</span></span>

<span data-ttu-id="c3d18-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="c3d18-2165">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="c3d18-2166">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="c3d18-2167">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="c3d18-2168">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="c3d18-2169">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="c3d18-2170">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="c3d18-2171">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="c3d18-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="c3d18-2172">GetChildren</span></span>

<span data-ttu-id="c3d18-2173">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="c3d18-2174">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="c3d18-2174">Exists</span></span>

<span data-ttu-id="c3d18-2175">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="c3d18-2176">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="c3d18-2177">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="c3d18-2177">Bind to an object graph</span></span>

<span data-ttu-id="c3d18-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="c3d18-2179">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="c3d18-2180">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="c3d18-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="c3d18-2181">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="c3d18-2182">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="c3d18-2183">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="c3d18-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="c3d18-2185">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="c3d18-2186">Der folgende Code zeigt, wie `Get<T>` in Bezug auf das vorherige Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="c3d18-2187">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="c3d18-2187">Bind an array to a class</span></span>

<span data-ttu-id="c3d18-2188">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="c3d18-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="c3d18-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c3d18-2190">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="c3d18-2191">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2191">Binding is provided by convention.</span></span> <span data-ttu-id="c3d18-2192">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="c3d18-2193">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="c3d18-2193">**In-memory array processing**</span></span>

<span data-ttu-id="c3d18-2194">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="c3d18-2195">Key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2195">Key</span></span>             | <span data-ttu-id="c3d18-2196">Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2196">Value</span></span>  |
| :---
<span data-ttu-id="c3d18-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2198">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2199">'Identity'</span></span>
- <span data-ttu-id="c3d18-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2201">'Razor'</span></span>
- <span data-ttu-id="c3d18-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2204">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2205">'Identity'</span></span>
- <span data-ttu-id="c3d18-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2207">'Razor'</span></span>
- <span data-ttu-id="c3d18-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2210">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2211">'Identity'</span></span>
- <span data-ttu-id="c3d18-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2213">'Razor'</span></span>
- <span data-ttu-id="c3d18-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2216">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2217">'Identity'</span></span>
- <span data-ttu-id="c3d18-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2219">'Razor'</span></span>
- <span data-ttu-id="c3d18-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="c3d18-2222">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="c3d18-2223">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="c3d18-2224">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="c3d18-2225">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="c3d18-2226">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="c3d18-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann ebenfalls verwendet werden, was zu kompakteren Code führt:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="c3d18-2228">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="c3d18-2229">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="c3d18-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c3d18-2230">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="c3d18-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2232">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2233">'Identity'</span></span>
- <span data-ttu-id="c3d18-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2235">'Razor'</span></span>
- <span data-ttu-id="c3d18-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2238">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2239">'Identity'</span></span>
- <span data-ttu-id="c3d18-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2241">'Razor'</span></span>
- <span data-ttu-id="c3d18-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2244">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2245">'Identity'</span></span>
- <span data-ttu-id="c3d18-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2247">'Razor'</span></span>
- <span data-ttu-id="c3d18-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2250">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2251">'Identity'</span></span>
- <span data-ttu-id="c3d18-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2253">'Razor'</span></span>
- <span data-ttu-id="c3d18-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2256">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2257">'Identity'</span></span>
- <span data-ttu-id="c3d18-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2259">'Razor'</span></span>
- <span data-ttu-id="c3d18-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2262">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2263">'Identity'</span></span>
- <span data-ttu-id="c3d18-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2265">'Razor'</span></span>
- <span data-ttu-id="c3d18-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2268">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2269">'Identity'</span></span>
- <span data-ttu-id="c3d18-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2271">'Razor'</span></span>
- <span data-ttu-id="c3d18-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2274">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2275">'Identity'</span></span>
- <span data-ttu-id="c3d18-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2277">'Razor'</span></span>
- <span data-ttu-id="c3d18-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2280">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2281">'Identity'</span></span>
- <span data-ttu-id="c3d18-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2283">'Razor'</span></span>
- <span data-ttu-id="c3d18-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2286">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2287">'Identity'</span></span>
- <span data-ttu-id="c3d18-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2289">'Razor'</span></span>
- <span data-ttu-id="c3d18-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2292">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2293">'Identity'</span></span>
- <span data-ttu-id="c3d18-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2295">'Razor'</span></span>
- <span data-ttu-id="c3d18-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2298">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2299">'Identity'</span></span>
- <span data-ttu-id="c3d18-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2301">'Razor'</span></span>
- <span data-ttu-id="c3d18-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2304">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2305">'Identity'</span></span>
- <span data-ttu-id="c3d18-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2307">'Razor'</span></span>
- <span data-ttu-id="c3d18-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2310">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2311">'Identity'</span></span>
- <span data-ttu-id="c3d18-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2313">'Razor'</span></span>
- <span data-ttu-id="c3d18-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2316">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2317">'Identity'</span></span>
- <span data-ttu-id="c3d18-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2319">'Razor'</span></span>
- <span data-ttu-id="c3d18-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2322">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2323">'Identity'</span></span>
- <span data-ttu-id="c3d18-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2325">'Razor'</span></span>
- <span data-ttu-id="c3d18-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2328">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2329">'Identity'</span></span>
- <span data-ttu-id="c3d18-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2331">'Razor'</span></span>
- <span data-ttu-id="c3d18-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2334">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2335">'Identity'</span></span>
- <span data-ttu-id="c3d18-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2337">'Razor'</span></span>
- <span data-ttu-id="c3d18-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2340">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2341">'Identity'</span></span>
- <span data-ttu-id="c3d18-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2343">'Razor'</span></span>
- <span data-ttu-id="c3d18-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2346">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2347">'Identity'</span></span>
- <span data-ttu-id="c3d18-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2349">'Razor'</span></span>
- <span data-ttu-id="c3d18-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2352">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2353">'Identity'</span></span>
- <span data-ttu-id="c3d18-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2355">'Razor'</span></span>
- <span data-ttu-id="c3d18-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2358">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2359">'Identity'</span></span>
- <span data-ttu-id="c3d18-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2361">'Razor'</span></span>
- <span data-ttu-id="c3d18-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="c3d18-2364">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="c3d18-2365">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="c3d18-2366">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="c3d18-2367">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="c3d18-2368">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="c3d18-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="c3d18-2370">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="c3d18-2371">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="c3d18-2372">Key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2372">Key</span></span>             | <span data-ttu-id="c3d18-2373">Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2373">Value</span></span>  |
| :---
<span data-ttu-id="c3d18-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2375">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2376">'Identity'</span></span>
- <span data-ttu-id="c3d18-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2378">'Razor'</span></span>
- <span data-ttu-id="c3d18-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2381">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2382">'Identity'</span></span>
- <span data-ttu-id="c3d18-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2384">'Razor'</span></span>
- <span data-ttu-id="c3d18-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2387">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2388">'Identity'</span></span>
- <span data-ttu-id="c3d18-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2390">'Razor'</span></span>
- <span data-ttu-id="c3d18-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2393">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2394">'Identity'</span></span>
- <span data-ttu-id="c3d18-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2396">'Razor'</span></span>
- <span data-ttu-id="c3d18-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="c3d18-2399">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="c3d18-2400">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="c3d18-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c3d18-2401">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="c3d18-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2403">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2404">'Identity'</span></span>
- <span data-ttu-id="c3d18-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2406">'Razor'</span></span>
- <span data-ttu-id="c3d18-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2409">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2410">'Identity'</span></span>
- <span data-ttu-id="c3d18-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2412">'Razor'</span></span>
- <span data-ttu-id="c3d18-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2415">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2416">'Identity'</span></span>
- <span data-ttu-id="c3d18-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2418">'Razor'</span></span>
- <span data-ttu-id="c3d18-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2421">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2422">'Identity'</span></span>
- <span data-ttu-id="c3d18-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2424">'Razor'</span></span>
- <span data-ttu-id="c3d18-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2427">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2428">'Identity'</span></span>
- <span data-ttu-id="c3d18-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2430">'Razor'</span></span>
- <span data-ttu-id="c3d18-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2433">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2434">'Identity'</span></span>
- <span data-ttu-id="c3d18-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2436">'Razor'</span></span>
- <span data-ttu-id="c3d18-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2439">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2440">'Identity'</span></span>
- <span data-ttu-id="c3d18-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2442">'Razor'</span></span>
- <span data-ttu-id="c3d18-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2445">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2446">'Identity'</span></span>
- <span data-ttu-id="c3d18-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2448">'Razor'</span></span>
- <span data-ttu-id="c3d18-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2451">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2452">'Identity'</span></span>
- <span data-ttu-id="c3d18-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2454">'Razor'</span></span>
- <span data-ttu-id="c3d18-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2457">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2458">'Identity'</span></span>
- <span data-ttu-id="c3d18-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2460">'Razor'</span></span>
- <span data-ttu-id="c3d18-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2463">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2464">'Identity'</span></span>
- <span data-ttu-id="c3d18-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2466">'Razor'</span></span>
- <span data-ttu-id="c3d18-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2469">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2470">'Identity'</span></span>
- <span data-ttu-id="c3d18-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2472">'Razor'</span></span>
- <span data-ttu-id="c3d18-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2475">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2476">'Identity'</span></span>
- <span data-ttu-id="c3d18-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2478">'Razor'</span></span>
- <span data-ttu-id="c3d18-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2481">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2482">'Identity'</span></span>
- <span data-ttu-id="c3d18-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2484">'Razor'</span></span>
- <span data-ttu-id="c3d18-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2487">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2488">'Identity'</span></span>
- <span data-ttu-id="c3d18-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2490">'Razor'</span></span>
- <span data-ttu-id="c3d18-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2493">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2494">'Identity'</span></span>
- <span data-ttu-id="c3d18-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2496">'Razor'</span></span>
- <span data-ttu-id="c3d18-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2499">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2500">'Identity'</span></span>
- <span data-ttu-id="c3d18-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2502">'Razor'</span></span>
- <span data-ttu-id="c3d18-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2505">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2506">'Identity'</span></span>
- <span data-ttu-id="c3d18-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2508">'Razor'</span></span>
- <span data-ttu-id="c3d18-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2511">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2512">'Identity'</span></span>
- <span data-ttu-id="c3d18-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2514">'Razor'</span></span>
- <span data-ttu-id="c3d18-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2517">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2518">'Identity'</span></span>
- <span data-ttu-id="c3d18-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2520">'Razor'</span></span>
- <span data-ttu-id="c3d18-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2523">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2524">'Identity'</span></span>
- <span data-ttu-id="c3d18-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2526">'Razor'</span></span>
- <span data-ttu-id="c3d18-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2529">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2530">'Identity'</span></span>
- <span data-ttu-id="c3d18-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2532">'Razor'</span></span>
- <span data-ttu-id="c3d18-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="c3d18-2535">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="c3d18-2535">**JSON array processing**</span></span>

<span data-ttu-id="c3d18-2536">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="c3d18-2537">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="c3d18-2538">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="c3d18-2539">Key</span><span class="sxs-lookup"><span data-stu-id="c3d18-2539">Key</span></span>                     | <span data-ttu-id="c3d18-2540">Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2540">Value</span></span>  |
| ---
<span data-ttu-id="c3d18-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2542">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2543">'Identity'</span></span>
- <span data-ttu-id="c3d18-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2545">'Razor'</span></span>
- <span data-ttu-id="c3d18-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2548">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2549">'Identity'</span></span>
- <span data-ttu-id="c3d18-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2551">'Razor'</span></span>
- <span data-ttu-id="c3d18-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2554">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2555">'Identity'</span></span>
- <span data-ttu-id="c3d18-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2557">'Razor'</span></span>
- <span data-ttu-id="c3d18-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2560">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2561">'Identity'</span></span>
- <span data-ttu-id="c3d18-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2563">'Razor'</span></span>
- <span data-ttu-id="c3d18-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2566">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2567">'Identity'</span></span>
- <span data-ttu-id="c3d18-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2569">'Razor'</span></span>
- <span data-ttu-id="c3d18-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2572">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2573">'Identity'</span></span>
- <span data-ttu-id="c3d18-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2575">'Razor'</span></span>
- <span data-ttu-id="c3d18-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2578">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2579">'Identity'</span></span>
- <span data-ttu-id="c3d18-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2581">'Razor'</span></span>
- <span data-ttu-id="c3d18-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2584">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2585">'Identity'</span></span>
- <span data-ttu-id="c3d18-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2587">'Razor'</span></span>
- <span data-ttu-id="c3d18-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2590">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2591">'Identity'</span></span>
- <span data-ttu-id="c3d18-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2593">'Razor'</span></span>
- <span data-ttu-id="c3d18-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="c3d18-2596">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="c3d18-2597">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="c3d18-2598">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="c3d18-2599">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="c3d18-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="c3d18-2600">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="c3d18-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="c3d18-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2602">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2603">'Identity'</span></span>
- <span data-ttu-id="c3d18-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2605">'Razor'</span></span>
- <span data-ttu-id="c3d18-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2608">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2609">'Identity'</span></span>
- <span data-ttu-id="c3d18-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2611">'Razor'</span></span>
- <span data-ttu-id="c3d18-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2614">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2615">'Identity'</span></span>
- <span data-ttu-id="c3d18-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2617">'Razor'</span></span>
- <span data-ttu-id="c3d18-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2620">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2621">'Identity'</span></span>
- <span data-ttu-id="c3d18-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2623">'Razor'</span></span>
- <span data-ttu-id="c3d18-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2626">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2627">'Identity'</span></span>
- <span data-ttu-id="c3d18-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2629">'Razor'</span></span>
- <span data-ttu-id="c3d18-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2632">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2633">'Identity'</span></span>
- <span data-ttu-id="c3d18-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2635">'Razor'</span></span>
- <span data-ttu-id="c3d18-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2638">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2639">'Identity'</span></span>
- <span data-ttu-id="c3d18-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2641">'Razor'</span></span>
- <span data-ttu-id="c3d18-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2644">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2645">'Identity'</span></span>
- <span data-ttu-id="c3d18-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2647">'Razor'</span></span>
- <span data-ttu-id="c3d18-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2650">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2651">'Identity'</span></span>
- <span data-ttu-id="c3d18-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2653">'Razor'</span></span>
- <span data-ttu-id="c3d18-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2656">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2657">'Identity'</span></span>
- <span data-ttu-id="c3d18-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2659">'Razor'</span></span>
- <span data-ttu-id="c3d18-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2662">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2663">'Identity'</span></span>
- <span data-ttu-id="c3d18-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2665">'Razor'</span></span>
- <span data-ttu-id="c3d18-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2668">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2669">'Identity'</span></span>
- <span data-ttu-id="c3d18-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2671">'Razor'</span></span>
- <span data-ttu-id="c3d18-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2674">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2675">'Identity'</span></span>
- <span data-ttu-id="c3d18-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2677">'Razor'</span></span>
- <span data-ttu-id="c3d18-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2680">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2681">'Identity'</span></span>
- <span data-ttu-id="c3d18-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2683">'Razor'</span></span>
- <span data-ttu-id="c3d18-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2686">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2687">'Identity'</span></span>
- <span data-ttu-id="c3d18-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2689">'Razor'</span></span>
- <span data-ttu-id="c3d18-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2692">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2693">'Identity'</span></span>
- <span data-ttu-id="c3d18-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2695">'Razor'</span></span>
- <span data-ttu-id="c3d18-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2698">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2699">'Identity'</span></span>
- <span data-ttu-id="c3d18-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2701">'Razor'</span></span>
- <span data-ttu-id="c3d18-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2704">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2705">'Identity'</span></span>
- <span data-ttu-id="c3d18-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2707">'Razor'</span></span>
- <span data-ttu-id="c3d18-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2710">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2711">'Identity'</span></span>
- <span data-ttu-id="c3d18-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2713">'Razor'</span></span>
- <span data-ttu-id="c3d18-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2716">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2717">'Identity'</span></span>
- <span data-ttu-id="c3d18-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2719">'Razor'</span></span>
- <span data-ttu-id="c3d18-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2722">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2723">'Identity'</span></span>
- <span data-ttu-id="c3d18-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2725">'Razor'</span></span>
- <span data-ttu-id="c3d18-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2728">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2729">'Identity'</span></span>
- <span data-ttu-id="c3d18-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2731">'Razor'</span></span>
- <span data-ttu-id="c3d18-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2734">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2735">'Identity'</span></span>
- <span data-ttu-id="c3d18-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2737">'Razor'</span></span>
- <span data-ttu-id="c3d18-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2740">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2741">'Identity'</span></span>
- <span data-ttu-id="c3d18-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2743">'Razor'</span></span>
- <span data-ttu-id="c3d18-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2746">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2747">'Identity'</span></span>
- <span data-ttu-id="c3d18-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2749">'Razor'</span></span>
- <span data-ttu-id="c3d18-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2752">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2753">'Identity'</span></span>
- <span data-ttu-id="c3d18-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2755">'Razor'</span></span>
- <span data-ttu-id="c3d18-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2758">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2759">'Identity'</span></span>
- <span data-ttu-id="c3d18-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2761">'Razor'</span></span>
- <span data-ttu-id="c3d18-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c3d18-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c3d18-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2764">'Blazor'</span></span>
- <span data-ttu-id="c3d18-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2765">'Identity'</span></span>
- <span data-ttu-id="c3d18-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="c3d18-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c3d18-2767">'Razor'</span></span>
- <span data-ttu-id="c3d18-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="c3d18-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="c3d18-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="c3d18-2770">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="c3d18-2770">Custom configuration provider</span></span>

<span data-ttu-id="c3d18-2771">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="c3d18-2772">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="c3d18-2773">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="c3d18-2774">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="c3d18-2775">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="c3d18-2776">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="c3d18-2777">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="c3d18-2778">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="c3d18-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="c3d18-2780">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="c3d18-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c3d18-2782">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="c3d18-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="c3d18-2784">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="c3d18-2785">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="c3d18-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="c3d18-2787">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="c3d18-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="c3d18-2789">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="c3d18-2790">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="c3d18-2790">Access configuration during startup</span></span>

<span data-ttu-id="c3d18-2791">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c3d18-2792">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="c3d18-2793">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="c3d18-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="c3d18-2794">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="c3d18-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="c3d18-2795">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="c3d18-2796">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="c3d18-2797">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="c3d18-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="c3d18-2798">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="c3d18-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="c3d18-2799">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c3d18-2800">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c3d18-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3d18-2801">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c3d18-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
