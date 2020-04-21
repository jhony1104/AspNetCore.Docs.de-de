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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument werden Techniken zum Speichern und Abrufen vertraulicher Daten während der Entwicklung einer ASP.NET Core-App auf einem Entwicklungscomputer erläutert. Speichern Sie niemals Kennwörter oder andere vertrauliche Daten im Quellcode. Produktionsgeheimnisse sollten nicht für die Entwicklung oder den Test verwendet werden. Geheimnisse sollten nicht mit der App bereitgestellt werden. Stattdessen sollten Geheimnisse in der Produktionsumgebung über eine kontrollierte Mittelart wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test- und Produktionsgeheimnisse beim [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration)speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um die Speicherung von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurationsquellen.

Betrachten Sie eine ASP.NET Core-Web-App, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine Standarddatenbankverbindungszeichenfolge ist in der Datei *appsettings.json* `DefaultConnection`des Projekts mit dem Schlüssel enthalten. Die Standardverbindungszeichenfolge ist für LocalDB, die im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der App-Bereitstellung kann der Schlüsselwert mit dem `DefaultConnection` Wert einer Umgebungsvariablen überschrieben werden. Die Umgebungsvariable kann die vollständige Verbindungszeichenfolge mit vertraulichen Anmeldeinformationen speichern.

> [!WARNING]
> Umgebungsvariablen werden in der Regel in einfachem, unverschlüsseltem Text gespeichert. Wenn der Computer oder Prozess kompromittiert ist, können nicht vertrauenswürdige Parteien auf Umgebungsvariablen zugreifen. Zusätzliche Maßnahmen zur Verhinderung der Offenlegung von Nutzergeheimnissen können erforderlich sein.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.NET Core-Projekts. In diesem Zusammenhang ist ein Element sensibler Daten ein Geheimobjekt der App. App-Geheimnisse werden an einem separaten Speicherort als der Projektbaum gespeichert. Die App-Geheimnisse sind einem bestimmten Projekt zugeordnet oder für mehrere Projekte freigegeben. Die App-Geheimnisse werden nicht in die Quellcodeverwaltung eingecheckt.

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten Geheimnisse nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Es ist nur für Entwicklungszwecke. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofilverzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>Funktionsweise des Secret Manager-Tools

Das Tool Secret Manager abstrahiert die Implementierungsdetails, z. B. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails zu kennen. Die Werte werden in einer JSON-Konfigurationsdatei in einem systemgeschützten Benutzerprofilordner auf dem lokalen Computer gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen Sie in den `<user_secrets_id>` vorherigen `UserSecretsId` Dateipfaden durch den in der *.csproj-Datei* angegebenen Wert.

Schreiben Sie keinen Code, der vom Speicherort oder Demformat der mit dem Secret Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Beispielsweise werden die geheimen Werte nicht verschlüsselt, sondern könnten in der Zukunft vorliegen.

## <a name="enable-secret-storage"></a>Geheime Speicherung aktivieren

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Das Secret Manager-Tool enthält einen `init` Befehl in .NET Core SDK 3.0.100 oder höher. Um Benutzergeheimnisse zu verwenden, führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet user-secrets init
```

Der vorherige Befehl `UserSecretsId` fügt `PropertyGroup` ein Element innerhalb einer der *.csproj-Datei* hinzu. Standardmäßig ist der innere `UserSecretsId` Text von eine GUID. Der innere Text ist beliebig, aber für das Projekt eindeutig.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie im Kontextmenü **Benutzergeheimnisse verwalten** aus. Diese Geste `UserSecretsId` fügt der *.csproj-Datei* ein Element hinzu, das mit einer GUID gefüllt ist.

## <a name="set-a-secret"></a>Ein Geheimnis setzen

Definieren Sie einen App-Geheimnis, der aus einem Schlüssel und seinem Wert besteht. Der geheime Schlüssel ist mit `UserSecretsId` dem Wert des Projekts verknüpft. Führen Sie beispielsweise den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel bezeichnet der `Movies` Doppelpunkt das Objektliteral mit einer `ServiceApiKey` Eigenschaft.

Das Secret Manager-Tool kann auch von anderen Verzeichnissen verwendet werden. Verwenden `--project` Sie die Option, um den Dateisystempfad einzugeben, an dem die *.csproj-Datei* vorhanden ist. Beispiel:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>JSON-Strukturabflachung in Visual Studio

Die Geste **Benutzergeheimnisse verwalten** von Visual Studio öffnet eine *secrets.json-Datei* im Texteditor. Ersetzen Sie den Inhalt von *secrets.json* durch die zu speichernden Schlüssel-Wert-Paare. Beispiel:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Die JSON-Struktur wird nach `dotnet user-secrets remove` Modifikationen über oder `dotnet user-secrets set`abgeflacht. Durch ausführen `dotnet user-secrets remove "Movies:ConnectionString"` wird z. B. das `Movies` Objektliteral reduziert. Die geänderte Datei ähnelt der folgenden:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer Geheimnisse

Eine Charge von Geheimnissen kann festgelegt werden, indem JSON an den `set` Befehl gerohrt wird. Im folgenden Beispiel wird der Inhalt der *datei input.json* an den `set` Befehl weitergegeben.

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Zugriff auf ein Geheimnis

Die [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) bietet Zugriff auf Secret Manager-Geheimnisse.

In ASP.NET Core 2.0 oder höher wird die Konfigurationsquelle für Benutzergeheimnisse automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> um eine neue Instanz des Hosts mit vorkonfigurierten Standardeinstellungen zu initialisieren. `CreateDefaultBuilder`Anrufe, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wenn <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>die ist:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurationsquelle `Startup` für Benutzergeheimnisse explizit hinzu, indem Sie den Konstruktor aufrufen. <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> Rufen `AddUserSecrets` Sie nur auf, wenn die App in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

Benutzergeheimnisse können über `Configuration` die API abgerufen werden:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>Kartengeheimnisse zu einem POCO

Das Zuordnen eines gesamten Objektliterals zu einer POCO (eine einfache .NET-Klasse mit Eigenschaften) ist nützlich, um verwandte Eigenschaften zu aggregieren.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorherigen Geheimnisse einem POCO `Configuration` zuzuordnen, verwenden Sie die [Objektdiagrammbindungsfunktion](xref:fundamentals/configuration/index#bind-to-an-object-graph) der API. Der folgende Code ist `MovieSettings` an eine benutzerdefinierte `ServiceApiKey` POCO gebunden und greift auf den Eigenschaftswert zu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` `Movies:ServiceApiKey` und Geheimnisse werden den jeweiligen `MovieSettings`Eigenschaften zugeordnet in:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>String-Ersatz mit Geheimnissen

Das Speichern von Kennwörtern im Nur-Text ist unsicher. Beispielsweise kann eine in *appsettings.json* gespeicherte Datenbankverbindungszeichenfolge ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichererer Ansatz besteht darin, das Kennwort als geheim zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen `Password` Sie das Schlüssel-Wert-Paar aus der Verbindungszeichenfolge in *appsettings.json*. Beispiel:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimen kann <xref:System.Data.SqlClient.SqlConnectionStringBuilder> für <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> die Eigenschaft eines Objekts festgelegt werden, um die Verbindungszeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Liste der Geheimnisse

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets list
```

Die folgende Ausgabe wird angezeigt:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Im vorherigen Beispiel bezeichnet ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.json*.

## <a name="remove-a-single-secret"></a>Entfernen eines einzelnen geheimen Schlüssels

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Die Datei *secrets.json* der App wurde geändert, um das `MoviesConnectionString` Schlüsselwertpaar zu entfernen, das dem Schlüssel zugeordnet ist:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`zeigt die folgende Meldung an:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Entfernen Sie alle Geheimnisse

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets clear
```

Alle Benutzergeheimnisse für die App wurden aus der *Datei secrets.json* gelöscht:

```json
{}
```

Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf Secret Manager von IIS finden Sie in [diesem Problem.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument werden Techniken zum Speichern und Abrufen vertraulicher Daten während der Entwicklung einer ASP.NET Core-App auf einem Entwicklungscomputer erläutert. Speichern Sie niemals Kennwörter oder andere vertrauliche Daten im Quellcode. Produktionsgeheimnisse sollten nicht für die Entwicklung oder den Test verwendet werden. Geheimnisse sollten nicht mit der App bereitgestellt werden. Stattdessen sollten Geheimnisse in der Produktionsumgebung über eine kontrollierte Mittelart wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test- und Produktionsgeheimnisse beim [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration)speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um die Speicherung von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurationsquellen.

Betrachten Sie eine ASP.NET Core-Web-App, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine Standarddatenbankverbindungszeichenfolge ist in der Datei *appsettings.json* `DefaultConnection`des Projekts mit dem Schlüssel enthalten. Die Standardverbindungszeichenfolge ist für LocalDB, die im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der App-Bereitstellung kann der Schlüsselwert mit dem `DefaultConnection` Wert einer Umgebungsvariablen überschrieben werden. Die Umgebungsvariable kann die vollständige Verbindungszeichenfolge mit vertraulichen Anmeldeinformationen speichern.

> [!WARNING]
> Umgebungsvariablen werden in der Regel in einfachem, unverschlüsseltem Text gespeichert. Wenn der Computer oder Prozess kompromittiert ist, können nicht vertrauenswürdige Parteien auf Umgebungsvariablen zugreifen. Zusätzliche Maßnahmen zur Verhinderung der Offenlegung von Nutzergeheimnissen können erforderlich sein.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.NET Core-Projekts. In diesem Zusammenhang ist ein Element sensibler Daten ein Geheimobjekt der App. App-Geheimnisse werden an einem separaten Speicherort als der Projektbaum gespeichert. Die App-Geheimnisse sind einem bestimmten Projekt zugeordnet oder für mehrere Projekte freigegeben. Die App-Geheimnisse werden nicht in die Quellcodeverwaltung eingecheckt.

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten Geheimnisse nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Es ist nur für Entwicklungszwecke. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofilverzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>Funktionsweise des Secret Manager-Tools

Das Tool Secret Manager abstrahiert die Implementierungsdetails, z. B. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails zu kennen. Die Werte werden in einer JSON-Konfigurationsdatei in einem systemgeschützten Benutzerprofilordner auf dem lokalen Computer gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen Sie in den `<user_secrets_id>` vorherigen `UserSecretsId` Dateipfaden durch den in der *.csproj-Datei* angegebenen Wert.

Schreiben Sie keinen Code, der vom Speicherort oder Demformat der mit dem Secret Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Beispielsweise werden die geheimen Werte nicht verschlüsselt, sondern könnten in der Zukunft vorliegen.

## <a name="enable-secret-storage"></a>Geheime Speicherung aktivieren

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Um Benutzergeheimnisse zu `UserSecretsId` verwenden, `PropertyGroup` definieren Sie ein Element in einer *datei .csproj.* Der innere `UserSecretsId` Text von ist willkürlich, aber einzigartig für das Projekt. Entwickler generieren in der `UserSecretsId`Regel eine GUID für die .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie im Kontextmenü **Benutzergeheimnisse verwalten** aus. Diese Geste `UserSecretsId` fügt der *.csproj-Datei* ein Element hinzu, das mit einer GUID gefüllt ist.

## <a name="set-a-secret"></a>Ein Geheimnis setzen

Definieren Sie einen App-Geheimnis, der aus einem Schlüssel und seinem Wert besteht. Der geheime Schlüssel ist mit `UserSecretsId` dem Wert des Projekts verknüpft. Führen Sie beispielsweise den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel bezeichnet der `Movies` Doppelpunkt das Objektliteral mit einer `ServiceApiKey` Eigenschaft.

Das Secret Manager-Tool kann auch von anderen Verzeichnissen verwendet werden. Verwenden `--project` Sie die Option, um den Dateisystempfad einzugeben, an dem die *.csproj-Datei* vorhanden ist. Beispiel:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>JSON-Strukturabflachung in Visual Studio

Die Geste **Benutzergeheimnisse verwalten** von Visual Studio öffnet eine *secrets.json-Datei* im Texteditor. Ersetzen Sie den Inhalt von *secrets.json* durch die zu speichernden Schlüssel-Wert-Paare. Beispiel:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Die JSON-Struktur wird nach `dotnet user-secrets remove` Modifikationen über oder `dotnet user-secrets set`abgeflacht. Durch ausführen `dotnet user-secrets remove "Movies:ConnectionString"` wird z. B. das `Movies` Objektliteral reduziert. Die geänderte Datei ähnelt der folgenden:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer Geheimnisse

Eine Charge von Geheimnissen kann festgelegt werden, indem JSON an den `set` Befehl gerohrt wird. Im folgenden Beispiel wird der Inhalt der *datei input.json* an den `set` Befehl weitergegeben.

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Zugriff auf ein Geheimnis

Die [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) bietet Zugriff auf Secret Manager-Geheimnisse.

Wenn Ihr Projekt auf .NET Framework abzielt, installieren Sie das Paket [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.


In ASP.NET Core 2.0 oder höher wird die Konfigurationsquelle für Benutzergeheimnisse automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> um eine neue Instanz des Hosts mit vorkonfigurierten Standardeinstellungen zu initialisieren. `CreateDefaultBuilder`Anrufe, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wenn <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>die ist:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurationsquelle `Startup` für Benutzergeheimnisse explizit hinzu, indem Sie den Konstruktor aufrufen. <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> Rufen `AddUserSecrets` Sie nur auf, wenn die App in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

Benutzergeheimnisse können über `Configuration` die API abgerufen werden:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Kartengeheimnisse zu einem POCO

Das Zuordnen eines gesamten Objektliterals zu einer POCO (eine einfache .NET-Klasse mit Eigenschaften) ist nützlich, um verwandte Eigenschaften zu aggregieren.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorherigen Geheimnisse einem POCO `Configuration` zuzuordnen, verwenden Sie die [Objektdiagrammbindungsfunktion](xref:fundamentals/configuration/index#bind-to-an-object-graph) der API. Der folgende Code ist `MovieSettings` an eine benutzerdefinierte `ServiceApiKey` POCO gebunden und greift auf den Eigenschaftswert zu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` `Movies:ServiceApiKey` und Geheimnisse werden den jeweiligen `MovieSettings`Eigenschaften zugeordnet in:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>String-Ersatz mit Geheimnissen

Das Speichern von Kennwörtern im Nur-Text ist unsicher. Beispielsweise kann eine in *appsettings.json* gespeicherte Datenbankverbindungszeichenfolge ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichererer Ansatz besteht darin, das Kennwort als geheim zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen `Password` Sie das Schlüssel-Wert-Paar aus der Verbindungszeichenfolge in *appsettings.json*. Beispiel:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimen kann <xref:System.Data.SqlClient.SqlConnectionStringBuilder> für <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> die Eigenschaft eines Objekts festgelegt werden, um die Verbindungszeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Liste der Geheimnisse

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets list
```

Die folgende Ausgabe wird angezeigt:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Im vorherigen Beispiel bezeichnet ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.json*.

## <a name="remove-a-single-secret"></a>Entfernen eines einzelnen geheimen Schlüssels

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Die Datei *secrets.json* der App wurde geändert, um das `MoviesConnectionString` Schlüsselwertpaar zu entfernen, das dem Schlüssel zugeordnet ist:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Entfernen Sie alle Geheimnisse

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:

```dotnetcli
dotnet user-secrets clear
```

Alle Benutzergeheimnisse für die App wurden aus der *Datei secrets.json* gelöscht:

```json
{}
```

Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf Secret Manager von IIS finden Sie in [diesem Problem.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end