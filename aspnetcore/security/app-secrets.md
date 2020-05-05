---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen in Form von App-Geheimnissen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 7508aebcda4e14812140f13ece635428908a4abb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776681"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Dieses Dokument erläutert Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App auf einem Entwicklungs Computer. Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode. Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden. Geheimnisse sollten nicht mit der APP bereitgestellt werden. Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.

Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Datei " *appSettings. JSON* " `DefaultConnection`des Projekts mit dem Schlüssel enthalten. Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der APP-bereit `DefaultConnection` Stellung kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden. In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.

> [!WARNING]
> Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert. Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden. Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts. In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel. App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert. Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden. Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Dies ist nur für Entwicklungszwecke vorgesehen. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>So funktioniert das Secret Manager-Tool

Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen. Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen `<user_secrets_id>` Sie in den vorangehenden Dateipfaden durch `UserSecretsId` den Wert, der in der *csproj* -Datei angegeben ist.

Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.

## <a name="enable-secret-storage"></a>Aktivieren der geheimen Speicherung

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Das Secret Manager-Tool enthält `init` einen Befehl in .net Core SDK 3.0.100 oder höher. Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet user-secrets init
```

Mit dem vorangehenden Befehl `UserSecretsId` wird ein- `PropertyGroup` Element in einer der *csproj* -Datei hinzugefügt. Standardmäßig ist der innere Text von `UserSecretsId` eine GUID. Der innere Text ist willkürlich, aber für das Projekt eindeutig.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus. Diese Geste fügt der `UserSecretsId` *csproj* -Datei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.

## <a name="set-a-secret"></a>Festlegen eines Geheimnisses

Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht. Das Geheimnis wird dem `UserSecretsId` Wert des Projekts zugeordnet. Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, `Movies` dass ein Objektliteral `ServiceApiKey` mit einer-Eigenschaft ist.

Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden. Verwenden Sie `--project` die-Option, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist. Beispiel:

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

Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder `dotnet user-secrets set`vereinfacht. Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen `Movies` des Objektliterals reduziert. Die geänderte Datei ähnelt der folgenden:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer geheimer Schlüssel

Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an `set` den Befehl festgelegt werden. Im folgenden Beispiel wird der Inhalt der Datei " *Input. JSON* " an den `set` Befehl weitergeleitet.

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Auf einen geheimen Schlüssel zugreifen

Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.

Die Konfigurations Quelle für Benutzer Geheimnisse wird automatisch im Entwicklungsmodus hinzugefügt, wenn <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> das Projekt aufruft, um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren. `CreateDefaultBuilder`<xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> wird <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> aufgerufen, wenn Folgendes ist <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>explizit hinzu, indem Sie aufrufen. Wird `AddUserSecrets` nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Benutzer Geheimnisse können über die `Configuration` API abgerufen werden:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Zuordnen von Geheimnissen zu einem poco

Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden `Configuration` Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der API. Der folgende Code bindet an ein Benutzer `MovieSettings` definiertes poco und `ServiceApiKey` greift auf den-Eigenschafts Wert zu:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` geheim `Movies:ServiceApiKey` nisse und werden den entsprechenden Eigenschaften in `MovieSettings`zugeordnet:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zeichen folgen Ersetzung mit geheimen Schlüsseln

Das Speichern von Kenn Wörtern im Klartext ist unsicher. Eine in " *appSettings. JSON* " gespeicherte Daten bankverbindungs Zeichenfolge kann z. b. ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen Sie `Password` das Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appSettings. JSON*. Beispiel:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimnisses kann für die-Eigenschaft <xref:System.Data.SqlClient.SqlConnectionStringBuilder> eines- <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Objekts festgelegt werden, um die Verbindungs Zeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

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

Die Datei " *Secrets. JSON* " der App wurde geändert, um das Schlüssel-Wert-Paar `MoviesConnectionString` zu entfernen, das dem Schlüssel zugeordnet ist:

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

Wird `dotnet user-secrets list` ausgeführt, wird die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf den geheimen Hauptschlüssel von IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Dieses Dokument erläutert Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App auf einem Entwicklungs Computer. Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode. Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden. Geheimnisse sollten nicht mit der APP bereitgestellt werden. Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.

Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Datei " *appSettings. JSON* " `DefaultConnection`des Projekts mit dem Schlüssel enthalten. Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der APP-bereit `DefaultConnection` Stellung kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden. In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.

> [!WARNING]
> Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert. Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden. Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts. In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel. App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert. Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden. Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Dies ist nur für Entwicklungszwecke vorgesehen. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>So funktioniert das Secret Manager-Tool

Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen. Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen `<user_secrets_id>` Sie in den vorangehenden Dateipfaden durch `UserSecretsId` den Wert, der in der *csproj* -Datei angegeben ist.

Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.

## <a name="enable-secret-storage"></a>Aktivieren der geheimen Speicherung

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Definieren Sie ein `UserSecretsId` -Element in einer `PropertyGroup` -Datei der *csproj* -Datei, um Benutzer Geheimnisse zu verwenden. Der innere Text von `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig. Entwickler generieren in der Regel eine GUID `UserSecretsId`für das.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus. Diese Geste fügt der `UserSecretsId` *csproj* -Datei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.

## <a name="set-a-secret"></a>Festlegen eines Geheimnisses

Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht. Das Geheimnis wird dem `UserSecretsId` Wert des Projekts zugeordnet. Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, `Movies` dass ein Objektliteral `ServiceApiKey` mit einer-Eigenschaft ist.

Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden. Verwenden Sie `--project` die-Option, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist. Beispiel:

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

Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder `dotnet user-secrets set`vereinfacht. Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen `Movies` des Objektliterals reduziert. Die geänderte Datei ähnelt der folgenden:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer geheimer Schlüssel

Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an `set` den Befehl festgelegt werden. Im folgenden Beispiel wird der Inhalt der Datei " *Input. JSON* " an den `set` Befehl weitergeleitet.

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Auf einen geheimen Schlüssel zugreifen

Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.

Wenn das Projekt .NET Framework als Ziel hat, installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> das Projekt aufruft, um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren. `CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wird <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> aufgerufen, wenn Folgendes ist <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> explizit hinzu `Startup` , indem Sie im-Konstruktor aufrufen. Wird `AddUserSecrets` nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Benutzer Geheimnisse können über die `Configuration` API abgerufen werden:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Zuordnen von Geheimnissen zu einem poco

Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden `Configuration` Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der API. Der folgende Code bindet an ein Benutzer `MovieSettings` definiertes poco und `ServiceApiKey` greift auf den-Eigenschafts Wert zu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` geheim `Movies:ServiceApiKey` nisse und werden den entsprechenden Eigenschaften in `MovieSettings`zugeordnet:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zeichen folgen Ersetzung mit geheimen Schlüsseln

Das Speichern von Kenn Wörtern im Klartext ist unsicher. Eine in " *appSettings. JSON* " gespeicherte Daten bankverbindungs Zeichenfolge kann z. b. ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen Sie `Password` das Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appSettings. JSON*. Beispiel:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimnisses kann für die-Eigenschaft <xref:System.Data.SqlClient.SqlConnectionStringBuilder> eines- <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Objekts festgelegt werden, um die Verbindungs Zeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

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

Die Datei " *Secrets. JSON* " der App wurde geändert, um das Schlüssel-Wert-Paar `MoviesConnectionString` zu entfernen, das dem Schlüssel zugeordnet ist:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Wird `dotnet user-secrets list` ausgeführt, wird die folgende Meldung angezeigt:

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

Wird `dotnet user-secrets list` ausgeführt, wird die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf den geheimen Hauptschlüssel von IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end