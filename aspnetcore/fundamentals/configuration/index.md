---
title: Konfiguration in ASP.NET Core
author: guardrex
description: 'In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a>Konfiguration in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden. Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:

::: moniker range=">= aspnetcore-2.1"

* Azure Key Vault
* Befehlszeilenargumente
* Benutzerdefinierte Anbieter (installiert oder erstellt)
* Verzeichnisdateien
* Umgebungsvariablen
* Speicherinterne .NET Objekte
* Einstellungsdateien

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

* Azure Key Vault
* Befehlszeilenargumente
* Benutzerdefinierte Anbieter (installiert oder erstellt)
* Umgebungsvariablen
* Speicherinterne .NET Objekte
* Einstellungsdateien

::: moniker-end

::: moniker range="= aspnetcore-1.0"

* Befehlszeilenargumente
* Benutzerdefinierte Anbieter (installiert oder erstellt)
* Umgebungsvariablen
* Speicherinterne .NET Objekte
* Einstellungsdateien

::: moniker-end

Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte. Optionsmuster verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen. Weitere Informationen zum Verwenden von Optionsmustern finden Sie unter <xref:fundamentals/configuration/options>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-2.1"

Diese drei Pakete sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Diese drei Pakete sind im Metapaket [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) enthalten.

::: moniker-end

## <a name="host-vs-app-configuration"></a>Hostkonfiguration vs. App-Konfiguration

Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet. Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer. Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert. Schlüssel-Wert-Paare der Hostkonfiguration werden Teil der globalen App-Konfiguration. Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter [Hosting](xref:fundamentals/index#host).

## <a name="default-configuration"></a>Standardkonfiguration

Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf. `CreateDefaultBuilder` legt die Standardkonfiguration für die App fest.

* Die Konfiguration des Hosts wird durch Folgendes festgelegt:
  * Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).
  * Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).
* Die Konfiguration der App wird durch Folgendes festgelegt (in dieser Reihenfolge):
  * *appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).
  * *appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).
  * [Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.
  * Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).
  * Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).

Die Konfigurationsanbieter werden im Verlauf des Artikels erläutert. Weitere Informationen zum Host und zu `CreateDefaultBuilder` finden Sie unter <xref:fundamentals/host/web-host#set-up-a-host>.

## <a name="security"></a>Sicherheit

Verwenden Sie die folgenden Best Practices:

* Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.
* Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.
* Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.

Lesen Sie mehr über das [Verwenden von mehreren Umgebungen](xref:fundamentals/environments) und das Verwalten der [sicheren Speicherung von App-Geheimnissen bei der Entwicklung mit dem Geheimnis-Manager](xref:security/app-secrets) (mit Informationen zum Speichern vertraulicher Daten mit Umgebungsvariablen). Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern. Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stellt eine Option für die sichere Speicherung von App-Geheimnissen dar. Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hierarchische Konfigurationsdaten

Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.

Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:

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

Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten. Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden. Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben. `GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

## <a name="conventions"></a>Konventionen

Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.

Dateikonfigurationsanbieter können Konfigurationen erneut laden, wenn eine zugrunde liegende Einstellungsdatei nach dem App-Start geändert wird. Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.

<xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung. Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.

Konfigurationsschlüssel entsprechen den folgenden Konventionen:

* Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet. Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.
* Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.
* Hierarchische Schlüssel
  * Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.
  * In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen. Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und in einen Doppelpunkt konvertiert.
  * In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen. Sie müssen einen Code bereitstellen, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln. Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.

Konfigurationswerte entsprechen den folgenden Konventionen:

* Die Werte sind Zeichenfolgen.
* NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.

## <a name="providers"></a>Anbieter

Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.

::: moniker range=">= aspnetcore-2.1"

| Anbieter | Bereitstellung der Konfiguration über&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*) | Azure Key Vault |
| [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter |
| [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle |
| [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen |
| [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) |
| [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien |
| [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen |
| [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*) | Datei im Benutzerprofilverzeichnis |

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

| Anbieter | Bereitstellung der Konfiguration über&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*) | Azure Key Vault |
| [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter |
| [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle |
| [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen |
| [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) |
| [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen |
| [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*) | Datei im Benutzerprofilverzeichnis |

::: moniker-end

::: moniker range="= aspnetcore-1.0"

| Anbieter | Bereitstellung der Konfiguration über&hellip; |
| -------- | ----------------------------------- |
| [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter |
| [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle |
| [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen |
| [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) |
| [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen |
| [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*) | Datei im Benutzerprofilverzeichnis |

::: moniker-end

Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind. Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der Ihr Code sie anordnet. Ordnen Sie die Konfigurationsanbieter in Ihrem Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen.

Eine typische Konfigurationsanbietersequenz ist:

1. Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur in der Entwicklungsumgebung)
1. Umgebungsvariablen
1. Befehlszeilenargumente

In der Regel werden Befehlszeilen-Konfigurationsanbieter zuletzt in einer Reihe von Anbietern positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.

::: moniker range=">= aspnetcore-2.0"

Diese Anbieterreihenfolge wird beim Initialisieren eines neuen <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Elements mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>festgelegt. Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Diese Sequenz von Anbietern kann für die App (nicht für den Host) mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> und einem Aufruf der <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*>-Methode in `Startup` erstellt werden:

```csharp
public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
            reloadOnChange: true);

    var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));

    if (appAssembly != null)
    {
        builder.AddUserSecrets(appAssembly, optional: true);
    }

    builder.AddEnvironmentVariables();

    Configuration = builder.Build();
}

public IConfiguration Configuration { get; }

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IConfiguration>(Configuration);
}
```

Im vorhergehenden Beispiel werden der Umgebungsname (`env.EnvironmentName`) und der Name der App-Assembly (`env.ApplicationName`) von <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> bereitgestellt. Weitere Informationen finden Sie unter <xref:fundamentals/environments>. Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.
sein.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt werden:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

::: moniker-end

## <a name="command-line-configuration-provider"></a>Befehlszeilen-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.

Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.

::: moniker range=">= aspnetcore-2.0"

`AddCommandLine` wird automatisch aufgerufen, wenn Sie ein neues <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> initialisieren. Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` lädt außerdem:

* Optionale Konfiguration aus *appsettings.json* und *appsettings.{Environment}.json*.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)
* Umgebungsvariablen.

`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu. Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.

`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde. Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.

`AddCommandLine` wurde bereits von `CreateDefaultBuilder` aufgerufen. Wenn Sie die App-Konfiguration bereitstellen müssen und weiterhin in der Lage sein möchten, diese Konfiguration mit Befehlszeilenargumenten außer Kraft zu setzen, rufen Sie die zusätzlichen Anbieter der App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> auf und rufen `AddCommandLine` zuletzt auf.

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

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an.

`AddCommandLine` wurde bereits von `CreateDefaultBuilder` aufgerufen, als `UseConfiguration` aufgerufen wurde. Wenn Sie die App-Konfiguration bereitstellen müssen und weiterhin in der Lage sein möchten, diese Konfiguration mit Befehlszeilenargumenten außer Kraft zu setzen, rufen Sie die zusätzlichen Anbieter der App für einen `ConfigurationBuilder` auf und rufen `AddCommandLine` zuletzt auf.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call other providers here and call AddCommandLine last.
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Um die Befehlszeilenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Rufen Sie den Anbieter zuletzt auf, damit die während der Laufzeit übergebenen Befehlszeilenargumente die von anderen Konfigurationsanbietern bestimmte Konfiguration überschreiben können.

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

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

**Beispiel**

::: moniker range=">= aspnetcore-2.0"

Die 2.x-Beispiel-App nutzt die statische Hilfsmethode `CreateDefaultBuilder`, um den Host zu erstellen, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Die 1.x-Beispiel-App ruft <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> auf <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> auf.

::: moniker-end

1. Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.
1. Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.
1. Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.

### <a name="arguments"></a>Argumente

Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt. Der Wert kann NULL sein, wenn ein Gleichheitszeichen verwendet wird (z.B. `CommandLineKey=`).

| Schlüsselpräfix               | Beispiel                                                |
| ------------------------ | ------------------------------------------------------ |
| Ohne Präfix                | `CommandLineKey1=value1`                               |
| Zwei Gedankenstriche (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Schrägstrich (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.

Beispielbefehle:

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Switchmappings

Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen. Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.

Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft. Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben. Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.

Regeln für Schlüssel von Switchmappingwörterbüchern:

* Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.
* Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

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

Wie im vorherigen Beispiel gezeigt, darf der Aufruf von `CreateDefaultBuilder` keine Argumente übergeben, wenn Switchmappings verwendet werden. Der Aufruf `AddCommandLine` der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden. Wenn die Argumente einen zugeordneten Switch enthalten und an `CreateDefaultBuilder` übergeben werden, kann der `AddCommandLine`-Anbieter nicht mit <xref:System.FormatException> initialisiert werden. Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var switchMappings = new Dictionary<string, string>
            {
                { "-CLKey1", "CommandLineKey1" },
                { "-CLKey2", "CommandLineKey2" }
            };

        var config = new ConfigurationBuilder()
            .AddCommandLine(args, switchMappings)
            .Build();

        // Do not pass the args to CreateDefaultBuilder
        return WebHost.CreateDefaultBuilder()
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Wie im vorherigen Beispiel gezeigt, darf der Aufruf von `CreateDefaultBuilder` keine Argumente übergeben, wenn Switchmappings verwendet werden. Der Aufruf `AddCommandLine` der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden. Wenn die Argumente einen zugeordneten Switch enthalten und an `CreateDefaultBuilder` übergeben werden, kann der `AddCommandLine`-Anbieter nicht mit <xref:System.FormatException> initialisiert werden. Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    var switchMappings = new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    var config = new ConfigurationBuilder()
        .AddCommandLine(args, switchMappings)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .UseStartup<Startup>()
        .Start();

    using (host)
    {
        Console.ReadLine();
    }
}
```

::: moniker-end

Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.

| Key       | Wert             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.

| Key               | Wert    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Umgebungsvariablen-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.

Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Beim Verwenden hierarchischer Schlüssel in Umgebungsvariablen funktioniert ein Doppelpunkt (`:`) als Trennzeichen ggf. nicht auf allen Plattformen. Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und durch einen Doppelpunkt ersetzt.

[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Umgebungsvariablen-Konfigurationsanbieter überschreiben können. Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

::: moniker range=">= aspnetcore-2.0"

`AddEnvironmentVariables` wird für Umgebungsvariablen mit dem Präfix `ASPNETCORE_` automatisch aufgerufen, wenn Sie einen neuen <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> initialisieren. Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` lädt außerdem:

* App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.
* Optionale Konfiguration aus *appsettings.json* und *appsettings.{Environment}.json*.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)
* Befehlszeilenargumenten

Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde. Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.

Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf.

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

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rufen Sie die Erweiterungsmethode `AddEnvironmentVariables` für eine <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf. Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an.

Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            // Call additional providers here as needed.
            // Call AddEnvironmentVariables last if you need to allow environment
            // variables to override values from other providers.
            .AddEnvironmentVariables(prefix: "PREFIX_")
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

**Beispiel**

::: moniker range=">= aspnetcore-2.0"

Die 2.x-Beispiel-App nutzt die statische Hilfsmethode `CreateDefaultBuilder`, um den Host zu erstellen, die einen Aufruf von `AddEnvironmentVariables` enthält.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Die 1.x-Beispiel-App ruft `AddEnvironmentVariables` auf `ConfigurationBuilder` auf.

::: moniker-end

1. Führen Sie die Beispiel-App aus. Öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält. Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.

Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen, die folgendermaßen beginnen:

* ASPNETCORE_
* urls
* Protokollierung
* ENVIRONMENT
* contentRoot
* AllowedHosts
* applicationName
* COMMANDLINE

::: moniker range=">= aspnetcore-2.0"

Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Controllers/HomeController.cs* wie folgt:

::: moniker-end

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Präfixe

Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben. Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.

::: moniker range=">= aspnetcore-2.0"

Die statische Hilfsmethode `CreateDefaultBuilder` erstellt ein <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element, um den Host der App einzurichten. Wenn `WebHostBuilder` erstellt wird, befindet sich die Hostkonfiguration in Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.

::: moniker-end

**Präfixe für Verbindungszeichenfolgen**

Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden. Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.

| Präfix für Verbindungszeichenfolgen | Anbieter |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:

* Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.
* Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).

| Umgebungsvariablenschlüssel | Konvertierter Konfigurationsschlüssel | Anbieterkonfigurationseintrag                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | Konfigurationseintrag wurde nicht erstellt.                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:<br>Wert: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:<br>Wert: `System.Data.SqlClient`  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:<br>Wert: `System.Data.SqlClient`  |

## <a name="file-configuration-provider"></a>Dateikonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem. Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:

* [INI-Konfigurationsanbieter](#ini-configuration-provider)
* [JSON-Konfigurationsanbieter](#json-configuration-provider)
* [XML-Konfigurationsanbieter](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.

Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rufen Sie beim Aufrufen von `CreateDefaultBuilder` <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddIniFile("config.ini", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Ein allgemeines Beispiel einer INI-Konfigurationsdatei:

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

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.

Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

::: moniker range=">= aspnetcore-2.0"

`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie ein neues <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> initialisieren. Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:

* *appsettings.json* &ndash; Diese Datei wird zuerst gelesen. Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.
* *appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.

Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).

`CreateDefaultBuilder` lädt außerdem:

* Umgebungsvariablen.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)
* Befehlszeilenargumenten

Der JSON-Konfigurationsanbieter wird zuerst eingerichtet. Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Sie können die Erweiterungsmethode `AddJsonFile` auch direkt auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz aufrufen.

Rufen Sie beim Aufrufen von `CreateDefaultBuilder` <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("config.json", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

**Beispiel**

::: moniker range=">= aspnetcore-2.0"

Die 2.x-Beispiel-App nutzt die statische Hilfsmethode `CreateDefaultBuilder`, um den Host zu erstellen, die zwei Aufrufe von `AddJsonFile` enthält. Die Konfiguration wird aus *appsettings.json* und *appsettings.{Environment}.json* geladen.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Die 1.x-Beispiel-App ruft `AddJsonFile` zweimal auf `ConfigurationBuilder` auf. Die Konfiguration wird aus *appsettings.json* und *appsettings.{Environment}.json* geladen.

::: moniker-end

1. Führen Sie die Beispiel-App aus. Öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Die Ausgabe enthält je nach Umgebung Schlüssel-Wert-Paare für die in der Tabelle dargestellte Konfiguration. Protokollierungskonfigurationsschlüssel verwenden den Doppelpunkt (`:`) als hierarchisches Trennzeichen.

| Key                        | Entwicklungswert | Produktionswert |
| -------------------------- | :---------------: | :--------------: |
| Logging:LogLevel:System    | Information       | Information      |
| Logging:LogLevel:Microsoft | Information       | Information      |
| Logging:LogLevel:Default   | Debug             | Fehler            |
| AllowedHosts               | *                 | *                |

### <a name="xml-configuration-provider"></a>XML-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.

Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert. Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rufen Sie beim Aufrufen von `CreateDefaultBuilder` <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:

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

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:

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

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

Mit Attributen können Werte bereitgestellt werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* key:attribute
* section:key:attribute

::: moniker range=">= aspnetcore-2.1"

## <a name="key-per-file-configuration-provider"></a>Schlüssel-pro-Datei-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare. Der Schlüssel ist der Dateiname. Der Wert enthält den Inhalt der Datei. Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.

Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf. Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.

Überladungen geben Folgendes an:

* Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert
* Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis

Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet. Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

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

Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt. `SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

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

::: moniker-end

## <a name="memory-configuration-provider"></a>Speicherkonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.

Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.

::: moniker range=">= aspnetcore-2.1"

Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

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

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Rufen Sie beim Aufrufen von `CreateDefaultBuilder` <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

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
}
```

Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenden Sie die Konfiguration mit der <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>-Methode auf <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> an:

::: moniker-end

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

## <a name="getvalue"></a>GetValue

[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Wert aus der Konfiguration mit einem angegebenen Schlüssel, und konvertiert ihn in den angegebenen Typ. Eine Überladung erlaubt es Ihnen, einen Standardwert anzugeben, wenn der Schlüssel nicht gefunden wird.

Das folgende Beispiel extrahiert den Zeichenfolgenwert aus der Konfiguration mit dem Schlüssel `NumberKey`, gibt den Wert als `int` an und speichert den Wert in der Variablen `intValue`. Wenn `NumberKey` nicht im Konfigurationsschlüssel gefunden wird, erhält `intValue` den Standardwert `99`:

```csharp
var intValue = config.GetValue<int>("NumberKey", 99);
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren und Exists

Betrachten Sie für die folgenden Beispiele die JSON-Datei unten. Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:

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

Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel. `GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:

```csharp
var configSection = _config.GetSection("section1");
```

Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.

Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` gibt nie `null` zurück. Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.

Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt. Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.

### <a name="getchildren"></a>GetChildren

Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

::: moniker range=">= aspnetcore-2.0"

### <a name="exists"></a>Vorhanden

Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Im Falle der Beispieldaten ist `sectionExists` `false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.

::: moniker-end

## <a name="bind-to-a-class"></a>Binden an eine Klasse

Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.

Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten. `Bind` befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/starship.json)]

::: moniker-end

Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:

| Key                   | Wert                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constitution                                      |
| starship:length       | 304,8                                             |
| starship:commissioned | False                                             |
| trademark             | Paramount Pictures Corp. http://www.paramount.com |

Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf. Die `starship`-Schlüssel-Wert-Paaren sind isoliert. Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird. Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_starship)]

::: moniker-end

`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

## <a name="bind-to-an-object-graph"></a>Binden an ein Objektdiagramm

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden. `Bind` befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-xml[](index/samples/1.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden. Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:

::: moniker range=">= aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
viewModel.TvShow = tvShow;
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück. `Get<T>` ist praktischer als `Bind`. Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_tvshow)]

::: moniker-end

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet. `Get<T>` ist in ASP.NET Core 1.1 und höher verfügbar. `GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln. Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden. „Bind“ befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

> [!NOTE]
> Das Binden ist standardmäßig möglich. Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.

**In-Memory-Arrayverarbeitung**

Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.

| Key             | Wert  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=5-12,16)]

::: moniker-end

Das Array überspringt einen Wert für Index &num;3. Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.

In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

Die Konfigurationsdaten werden an das Objekt gebunden:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.

::: moniker range=">= aspnetcore-1.1"

Die [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_array)]

::: moniker-end

Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.

| `ArrayExample.Entries`-Index | `ArrayExample.Entries`-Wert |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`. Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet. Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.

Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt. Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

::: moniker range=">= aspnetcore-2.0"

In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Im `Startup`-Konstruktor:

```csharp
.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.

| Key             | Wert  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.

| `ArrayExample.Entries`-Index | `ArrayExample.Entries`-Wert |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON-Arrayverarbeitung**

Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt. In der folgenden Konfigurationsdatei ist `subsection` ein Array:

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/json_array.json)]

::: moniker-end

Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:

| Key                     | Wert  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`. Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.

| `JsonArrayExample.Subsection`-Index | `JsonArrayExample.Subsection`-Wert |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Benutzerdefinierter Konfigurationsanbieter

Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.

Der Anbieter weist die folgenden Merkmale auf:

* Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet. Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.
* Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration. Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.
* Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.

Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.

*Models/EFConfigurationValue.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.

*EFConfigurationProvider/EFConfigurationContext.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.

*EFConfigurationProvider/EFConfigurationSource.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.

*Extensions/EntityFrameworkExtensions.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=24)]

::: moniker-end

## <a name="access-configuration-during-startup"></a>Zugreifen auf die Konfiguration während des Starts

Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen. Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:

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

Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht

Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.

Auf einer Razor Pages-Seite:

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

In einer MVC-Ansicht:

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

## <a name="add-configuration-from-an-external-assembly"></a>Hinzufügen von Konfigurationen aus einer externen Assembly

Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/options>
* [Fundierte Einblicke in die Microsoft-Konfiguration](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/) (in englischer Sprache)
