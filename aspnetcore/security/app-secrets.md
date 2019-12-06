---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen in Form von App-Geheimnissen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: ef5cb120c15d349be744c401bd518e026ddf11e9
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880785"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument werden Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App erläutert. Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode. Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden. Geheimnisse sollten nicht mit der APP bereitgestellt werden. Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.

::: moniker range="<= aspnetcore-1.1"

Konfigurieren Sie das Lesen von Umgebungsvariablen Werten, indem Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> im `Startup`-Konstruktor aufrufen:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Datei " *appSettings. JSON* " des Projekts mit dem Schlüssel `DefaultConnection`enthalten. Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der APP-Bereitstellung kann der `DefaultConnection` Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden. In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.

> [!WARNING]
> Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert. Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden. Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Geheimer Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts. In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel. App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert. Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden. Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Dies ist nur für Entwicklungszwecke vorgesehen. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>So funktioniert das Secret Manager-Tool

Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen. Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` Wert, der in der *csproj* -Datei angegeben ist.

Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>Installieren des Secret Manager-Tools

Das Secret Manager-Tool ist mit dem .net Core-CLI in .net Core SDK 2.1.300 oder höher gebündelt. Für .net Core SDK Versionen vor 2.1.300 ist eine Tool Installation erforderlich.

> [!TIP]
> Führen Sie `dotnet --version` über eine Befehlsshell aus, um die installierte .net Core SDK Versionsnummer anzuzeigen.

Eine Warnung wird angezeigt, wenn die .net Core SDK, die verwendet wird, das Tool umfasst:

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

Installieren Sie das nuget-Paket [Microsoft. Extensions. secretmanager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) in Ihrem ASP.net Core Projekt. Beispiel:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

Führen Sie den folgenden Befehl in einer Befehlsshell aus, um die Tool Installation zu überprüfen:

```dotnetcli
dotnet user-secrets -h
```

Das Tool Secret Manager zeigt Beispiel Verwendung, Optionen und Hilfe für Befehle an:

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
> Sie müssen sich im gleichen Verzeichnis wie die *csproj* -Datei befinden, um Tools auszuführen, die in den `DotNetCliToolReference` Elementen der *csproj* -Datei definiert sind.

::: moniker-end

## <a name="enable-secret-storage"></a>Aktivieren der geheimen Speicherung

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

::: moniker range=">= aspnetcore-3.0"

Das Secret Manager-Tool enthält einen `init` Befehl in .net Core SDK 3.0.100 oder höher. Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet user-secrets init
```

Der vorherige Befehl fügt ein `UserSecretsId`-Element innerhalb einer `PropertyGroup` der *csproj* -Datei hinzu. Standardmäßig ist der innere Text `UserSecretsId` eine GUID. Der innere Text ist willkürlich, aber für das Projekt eindeutig.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Definieren Sie ein `UserSecretsId`-Element innerhalb einer `PropertyGroup` der *csproj* -Datei, um Benutzer Geheimnisse zu verwenden. Der innere Text `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig. Entwickler generieren in der Regel eine GUID für die `UserSecretsId`.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus. Diese Geste fügt der *csproj* -Datei ein `UserSecretsId` Element, das mit einer GUID aufgefüllt ist, hinzu.

## <a name="set-a-secret"></a>Festlegen eines Geheimnisses

Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht. Der geheime Schlüssel wird dem `UserSecretsId` Wert des Projekts zugeordnet. Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` ein Objektliteral mit einer `ServiceApiKey`-Eigenschaft ist.

Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden. Verwenden Sie die Option `--project`, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist. Beispiel:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Vereinfachen der JSON-Struktur in Visual Studio

Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet die Datei " *Secrets. JSON* " im Text-Editor. Ersetzen Sie den Inhalt von " *Secrets. JSON* " durch die Schlüssel-Wert-Paare, die gespeichert werden sollen. Beispiel:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder `dotnet user-secrets set`vereinfacht. Beispielsweise wird durch das Ausführen von `dotnet user-secrets remove "Movies:ConnectionString"` das `Movies` Objektliterals reduziert. Die geänderte Datei ähnelt der folgenden:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer geheimer Schlüssel

Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den `set`-Befehl festgelegt werden. Im folgenden Beispiel wird der Inhalt der Datei " *Input. JSON* " an den `set`-Befehl weitergeleitet.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Auf einen geheimen Schlüssel zugreifen

Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Wenn das Projekt .NET Framework als Ziel hat, installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> aufruft, um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren. `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> aufruft, wenn der <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>ist:

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im `Startup`-Konstruktor aufrufen. Wenn die app in der Entwicklungsumgebung ausgeführt wird, wenden Sie `AddUserSecrets` an, wie im folgenden Beispiel gezeigt:

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

Fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse mit einem <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im `Startup`-Konstruktor aufgerufen wird:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

Benutzer Geheimnisse können über die `Configuration`-API abgerufen werden:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>Zuordnen von Geheimnissen zu einem poco

Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie das [Objekt Graph-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Feature der `Configuration`-API. Der folgende Code bindet an eine benutzerdefinierte `MovieSettings` poco und greift auf den `ServiceApiKey`-Eigenschafts Wert zu:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

Die `Movies:ConnectionString`-und `Movies:ServiceApiKey` geheimen Schlüssel werden den entsprechenden Eigenschaften in `MovieSettings`zugeordnet:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zeichen folgen Ersetzung mit geheimen Schlüsseln

Das Speichern von Kenn Wörtern im Klartext ist unsicher. Eine in " *appSettings. JSON* " gespeicherte Daten bankverbindungs Zeichenfolge kann z. b. ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen Sie das `Password` Schlüssel/Wert-Paar aus der Verbindungs Zeichenfolge in *appSettings. JSON*. Beispiel:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimnisses kann für die <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>-Eigenschaft eines <xref:System.Data.SqlClient.SqlConnectionStringBuilder> Objekts festgelegt werden, um die Verbindungs Zeichenfolge abzuschließen:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>Auflisten der geheimen Schlüssel

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets list
```

Die folgende Ausgabe wird angezeigt:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in " *Secrets. JSON*" an.

## <a name="remove-a-single-secret"></a>Entfernen eines einzelnen geheimen Schlüssels

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Die Datei " *Secrets. JSON* " der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das mit dem `MoviesConnectionString` Schlüssel verknüpft ist:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Beim Ausführen `dotnet user-secrets list` wird die folgende Meldung angezeigt:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Alle geheimen Schlüssel entfernen

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets clear
```

Alle geheimen Benutzerschlüssel für die APP wurden aus der Datei " *Secrets. JSON* " gelöscht:

```json
{}
```

Beim Ausführen `dotnet user-secrets list` wird die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
