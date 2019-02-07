---
title: Azure Key Vault-Konfigurationsanbieter in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie mit der Azure Key Vault-Konfigurationsanbieter, so konfigurieren Sie eine app mithilfe von Name / Wert-Paaren, die zur Laufzeit geladen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
uid: security/key-vault-configuration
ms.openlocfilehash: d255321f6083747ce9b452e1efd4da5bc015bf64
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854431"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault-Konfigurationsanbieter in ASP.NET Core

Durch [Luke Latham](https://github.com/guardrex) und [Andrew Stanton-Nurse](https://github.com/anurse)

In diesem Dokument wird erläutert, wie Sie mit der [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Konfigurationsanbieter zum Laden von app-Konfigurationswerte aus Azure Key Vault-Geheimnissen. Azure Key Vault ist ein cloudbasierter Dienst, die hilft beim Schutz von kryptografischen Schlüsseln und Geheimnissen, die von apps und-Diensten verwendet werden. Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-apps:

* Steuern des Zugriffs auf sensible Konfigurationsdaten aus.
* 140-2 Level 2, die erfüllt die Anforderungen für FIPS überprüft die von Hardwaresicherheitsmodulen (HSM), wenn Konfigurationsdaten zu speichern.

Dieses Szenario ist für apps mit ASP.NET Core 2.1 oder höher verfügbar.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakete

Um die Azure Key Vault-Konfigurationsanbieter zu verwenden, fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Paket.

Übernehmen der [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) Szenario fügen Sie einen Paketverweis auf die [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) Paket.

> [!NOTE]
> Zum Zeitpunkt der schreiben, der neuesten stabile Version des `Microsoft.Azure.Services.AppAuthentication`, Version `1.0.3`, bietet Unterstützung für [vom System zugewiesene verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka). Unterstützung für *Benutzer zugewiesene Identitäten verwaltet* finden Sie in der `1.0.2-preview` Paket. In diesem Thema veranschaulicht die Verwendung von vom System verwaltet Identitäten und die bereitgestellten Beispiel-app verwendet Version `1.0.3` von der `Microsoft.Azure.Services.AppAuthentication` Paket.

## <a name="sample-app"></a>Beispiel-App

Die Beispiel-app ausgeführt wird, in einem von zwei Modi, die bestimmt, indem die `#define` Anweisung am Anfang der *"Program.cs"* Datei:

* `Basic` &ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Anwendungs-ID und Kennwort (geheimer Clientschlüssel), auf im schlüsseltresor gespeicherte Geheimnisse zugreifen. Bereitstellen der `Basic` -Version des Beispiels auf einem beliebigen Host kann eine ASP.NET Core-app bereitstellen. Befolgen Sie die Anweisungen in der [verwenden Anwendungs-ID und geheimer Clientschlüssel für nicht-Azure-gehostete apps](#use-application-id-and-client-secret-for-non-azure-hosted-apps) Abschnitt.
* `Managed` &ndash; Veranschaulicht, wie [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) zum Authentifizieren von der app in Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen, die in der app Code oder Konfiguration gespeichert. Verwendung von verwalteten Identitäten um zu authentifizieren, sind eine Azure AD-Anwendungs-ID und Kennwort (geheimer Clientschlüssel) nicht erforderlich. Die `Managed` -Version des Beispiels in Azure bereitgestellt werden muss. Befolgen Sie die Anweisungen in der [verwenden Sie die verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) Abschnitt.

Weitere Informationen zum Konfigurieren einer Beispiel-app mittels präprozessoranweisungen (`#define`), finden Sie unter <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Geheimen Speicher in der Entwicklungsumgebung

Legen Sie die Geheimnisse, die lokal mithilfe der [Secret Manager-Tool](xref:security/app-secrets). Geheimnisse werden geladen, wenn die Beispiel-app auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, aus dem lokalen Speicher für die Geheimnis-Manager.

Das Geheimnis-Manager-Tool erfordert eine `<UserSecretsId>` -Eigenschaft in der app-Projektdatei. Legen Sie den Wert der Eigenschaft (`{GUID}`) auf eine beliebige eindeutige GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Geheime Schlüssel werden als Name / Wert-Paaren erstellt. Verwenden von hierarchische Werten (Konfigurationsabschnitte) eine `:` (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index) Schlüssel Namen.

Der Geheimnis-Manager wird verwendet, über eine Befehlsshell, die für das inhaltsstammverzeichnis des Projekts geöffnet, in denen `{SECRET NAME}` ist der Name und `{SECRET VALUE}` ist der Wert:

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Führen Sie die folgenden Befehle in einer Befehlsshell aus Inhalt Stamm des Projekts, um die geheimen Schlüssel für die Beispiel-app festzulegen:

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Wenn diese vertraulichen Informationen befinden sich in Azure Key Vault in der [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt der `_dev` Suffix wird geändert, um `_prod`. Das Suffix bietet einen visuellen Hinweis in der Ausgabe der Anwendung, der angibt, der Quelle der Konfigurationswerte an.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Geheimen Speicher in der produktionsumgebung mit Azure Key Vault

Die Anweisungen unter dem [Schnellstart: Festlegen und Abrufen ein geheimen Schlüssels aus Azure Key Vault mithilfe der Azure CLI](/azure/key-vault/quick-create-cli) Thema werden hier zusammengefasst, für das Erstellen von Azure Key Vault, und Speichern von Geheimnissen, die von der Beispiel-app verwendet. Das Thema für Weitere Informationen finden Sie unter.

1. Öffnen mit einer der folgenden Methoden in Azure-Cloud-Shell die [Azure-Portal](https://portal.azure.com/):

   * Wählen Sie **ausprobieren** in der oberen rechten Ecke eines Codeblocks. Verwenden Sie die Zeichenfolge "Azure-CLI" in das Textfeld ein.
   * Öffnen Sie Cloud Shell in Ihrem Browser mit der **Cloud Shell starten** Schaltfläche.
   * Wählen Sie die **Cloud Shell** im auf die Schaltfläche in der oberen rechten Ecke des Azure-Portals.

   Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).

1. Wenn Sie bereits authentifiziert sind nicht, melden Sie sich mit den `az login` Befehl.

1. Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, in denen `{RESOURCE GROUP NAME}` ist der Ressourcengruppenname für die neue Ressourcengruppe und `{LOCATION}` ist die Azure-Region (Rechenzentrum):

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen eines schlüsseltresors in der Ressourcengruppe mit dem folgenden Befehl, in denen `{KEY VAULT NAME}` ist der Name für die neue Key Vault und `{LOCATION}` ist die Azure-Region (Rechenzentrum):

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie Geheimnisse im schlüsseltresor als Name / Wert-Paare.

   Geheime Azure Key Vault-Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt. Verwenden von hierarchische Werten (Konfigurationsabschnitte) `--` (zwei Bindestriche) als Trennzeichen. Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt über einen Unterschlüssel in zu begrenzen [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index), sind nicht zulässig in Key Vault-Instanz den geheimen Namen. Daher sind die beiden Striche verwendet und für einen Doppelpunkt ausgetauscht werden, wenn die geheimen Schlüssel in der app Konfiguration geladen werden.

   Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-app. Die Werte umfassen eine `_prod` Suffix zur Unterscheidung von den `_dev` suffix Werte in der Entwicklungsumgebung aus geheime Benutzerschlüssel. Ersetzen Sie dies `{KEY VAULT NAME}` durch den Namen des schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a>Verwenden Sie Anwendungs-ID und geheimer Clientschlüssel für nicht-Azure gehosteten apps

Konfigurieren von Azure AD, Azure Key Vault, und der app, um eine Anwendungs-ID und Kennwort (geheimer Clientschlüssel) verwenden, um in einen schlüsseltresor authentifizieren **Wenn die app gehostet wird, außerhalb von Azure**.

> [!NOTE]
> Mithilfe einer Anwendungs-ID und Kennwort (geheimer Clientschlüssel) für apps, die in Azure gehostet wird zwar unterstützt, es wird empfohlen, [verwaltet Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer app in Azure. Verwaltete Identitäten erfordert das Speichern von Anmeldeinformationen in der app oder der Konfiguration, damit es im Allgemeinen sicherer Ansatz angesehen wird.

Die Beispiel-app verwendet eine Anwendungs-ID und ein Kennwort (geheimer Clientschlüssel) Wenn die `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Basic`.

1. Registrieren Sie einer app in Azure AD und herzustellen Sie ein Kennwort (geheimer Clientschlüssel) für die app Identität.
1. Der Name des schlüsseltresors, Anwendungs-ID und Kennwort/geheimer Clientschlüssel der App Store *"appSettings.JSON"* Datei.
1. Navigieren Sie zu **Schlüsseltresore** im Azure-Portal.
1. Wählen Sie die Key Vault-Instanz, die Sie in erstellt die [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt.
1. Wählen Sie **Zugriffsrichtlinien**.
1. Wählen Sie **neu hinzufügen**.
1. Wählen Sie **Prinzipal auswählen** , und wählen Sie nach dem Namen die registrierte Anwendung. Wählen Sie die **wählen** Schaltfläche.
1. Open **Berechtigungen für Geheimnis** , und geben Sie die app mit **erhalten** und **Liste** Berechtigungen.
1. Klicken Sie auf **OK**.
1. Klicken Sie auf **Speichern**.
1. Bereitstellen der app an.

Die `Basic` Beispiel-app erhält die Konfigurationswerte aus `IConfigurationRoot` mit dem gleichen Namen wie den Namen des geheimen Schlüssels:

* Nicht-hierarchischen Werten: Der Wert für `SecretName` wird abgerufen, mit `config["SecretName"]`.
* Hierarchische Werte (Abschnitte): Verwendung `:` (Doppelpunkt)-Notation oder der `GetSection` -Erweiterungsmethode. Verwenden Sie einen dieser Ansätze, um den Konfigurationswert zu erhalten:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Die app ruft `AddAzureKeyVault` mit Werten, die vom der *"appSettings.JSON"* Datei:

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

Beispielwerte:

* Name des schlüsseltresors: `contosovault`
* Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* Kennwort: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen. In der Entwicklungsumgebung, laden Sie geheime Werte mit den `_dev` Suffix. In der produktionsumgebung, laden Sie die Werte mit den `_prod` Suffix.

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

**Eine app in Azure bereitgestellten** profitieren von [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview), die die app für die Authentifizierung mit Azure Key Vault ermöglicht die Verwendung von Azure AD-Authentifizierung ohne Anmeldeinformationen (Anwendungs-ID und Password/Client Geheimnis) in der app gespeichert.

Die Beispiel-app verwaltete Identitäten verwendet, für die Azure-Ressourcen bei der `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Managed`.

Geben Sie den Namen des schlüsseltresors in der app *"appSettings.JSON"* Datei. Die Beispiel-app nicht erforderlich, eine Anwendungs-ID und Kennwort (geheimer Clientschlüssel) bei Festlegung auf den `Managed` Version, sodass Sie die Konfigurationseinträge ignorieren können. Die app in Azure bereitgestellt wird, und Azure authentifiziert die app für den Zugriff auf Azure Key Vault verwenden nur den Namen des schlüsseltresors in gespeicherten die *"appSettings.JSON"* Datei.

Stellen Sie die Beispiel-app in Azure App Service bereit.

Eine app in Azure App Service bereitgestellt wird automatisch in Azure AD registriert, wenn der Dienst erstellt wird. Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung in den folgenden Befehl aus. Die Objekt-ID wird im Azure-Portal angezeigt, auf die **Identität** Bereich des App Service.

Geben Sie mithilfe von Azure-Befehlszeilenschnittstelle und der app Objekt-ID der app mit `list` und `get` Berechtigungen für den schlüsseltresor zugreifen:

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**Die app neu starten** mithilfe von Azure-Befehlszeilenschnittstelle, PowerShell oder Azure-Portal.

Die Beispiel-app:

* Erstellt eine Instanz der `AzureServiceTokenProvider` Klasse, ohne dass eine Verbindungszeichenfolge. Wenn eine Verbindungszeichenfolge nicht angegeben wird, versucht der Anbieter, um ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen zu erhalten.
* Ein neues `KeyVaultClient` wird erstellt, mit der `AzureServiceTokenProvider` Instanz-token-Rückruf.
* Die `KeyVaultClient` Instanz wird verwendet, mit einer standardmäßigen Implementierung von `IKeyVaultSecretManager` , lädt alle geheime Werte und Double-Wert-Bindestriche ersetzt (`--`) mit Doppelpunkten (`:`) im Schlüsselnamen.

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen. In der Entwicklungsumgebung geheime Werte haben die `_dev` suffix, weil sie von geheime Benutzerschlüssel bereitgestellt werden. In der produktionsumgebung, laden Sie die Werte mit den `_prod` suffix, weil sie von Azure Key Vault bereitgestellt werden.

Wenn Sie erhalten eine `Access denied` Fehler, bestätigen Sie, dass die app bei Azure AD registriert und werden, Zugriff auf den schlüsseltresor bereitgestellt. Vergewissern Sie sich, dass der Dienst in Azure neu gestartet haben.

## <a name="use-a-key-name-prefix"></a>Verwenden Sie ein Präfix Schlüsselname

`AddAzureKeyVault` bietet eine Überladung, die eine Implementierung von akzeptiert `IKeyVaultSecretManager`, wodurch Sie steuern, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden. Beispielsweise können Sie implementieren die Schnittstelle zum Laden der geheime Werte basierend auf einem Präfixwert, die, den Sie beim Starten der app bereitstellen. Dadurch können Sie z. B., um Geheimnisse, die basierend auf der Version der app geladen werden.

> [!WARNING]
> Verwenden Sie keine Präfixe auf Key Vault-Geheimnisse, geheime Schlüssel für mehrere apps in dem gleichen schlüsseltresor zu platzieren oder environmental Geheimnisse zu setzen (z. B. *Entwicklung* im Vergleich zu *Produktion* Geheimnisse) in der gleichen Tresor. Es wird empfohlen, dass verschiedene apps und Entwicklungs-und produktionsumgebungen verwenden Sie separate Key Vault-Instanzen, um app-Umgebungen für das Höchstmaß an Sicherheit zu isolieren.

Im folgenden Beispiel wird hergestellt, ein geheimen Schlüssel im Schlüssel-Tresor (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Punkte sind nicht zulässig, in Key Vault-Instanz den geheimen Namen). Dieser geheime Schlüssel stellt ein app-Geheimnis für die app-Version 5.0.0.0 dar. Für eine andere Version der app 5.1.0.0, ein geheimen Schlüssel auf den Schlüssel hinzugefügt wird-Tresor (und mit dem Geheimnis-Manager-Tool) für `5100-AppSecret`. Jede Version der app lädt seine mit versionsverwaltung durch das geheimen Wert in seine Konfiguration als `AppSecret`, Ausblasegerät aus der Version, die den geheimen Schlüssel geladen.

`AddAzureKeyVault` wird aufgerufen, mit einem benutzerdefinierten `IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

Die Werte für den Namen des schlüsseltresors, Anwendungs-ID und Kennwort (geheimer Clientschlüssel) werden bereitgestellt, durch die *"appSettings.JSON"* Datei:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Beispielwerte:

* Name des schlüsseltresors: `contosovault`
* Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* Kennwort: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

Die `IKeyVaultSecretManager` Implementierung reagiert wird, auf die Version-Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in der Konfiguration zu laden:

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

Die `Load` Methode wird aufgerufen, indem ein anbieteralgorithmus, der durchläuft die Vault-Geheimnissen, um diejenigen zu finden, die das Version-Präfix besitzen. Wenn ein Präfix Version wurden gefunden, die `Load`, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des Namen des geheimen Schlüssels zurückzugeben. Es entfernt das Version-Präfix aus der Name des Geheimnisses und gibt den Rest der Namen des geheimen Schlüssels für das Laden von in der app-Konfiguration-Name-Wert-Paaren.

Bei diesem Ansatz implementiert wird:

1. Die Version des in der app-Projektdatei angegeben. Im folgenden Beispiel wird die Version des zum festgelegt `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Überprüfen Sie, ob eine `<UserSecretsId>` Eigenschaft ist in der app Projektdatei vorhanden, in denen `{GUID}` ist eine benutzerdefinierte GUID:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Secret Manager-Tool](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Geheime Schlüssel werden in Azure Key Vault gespeichert, mit den folgenden Azure CLI-Befehlen:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Wenn die app ausgeführt wird, werden die Key Vault-Geheimnisse geladen. Der geheime Zeichenfolge für `5000-AppSecret` wird abgeglichen, um die Version des in der app-Projektdatei angegeben (`5.0.0.0`).

1. Die Version `5000` (mit den Bindestrich), wird von den Namen des Schlüssels entfernt. In der app, die beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` lädt das Geheimnis.

1. Wenn die Version des in der Projektdatei geändert wird `5.1.0.0` und die app erneut ausgeführt wird, ist der geheime zurückgegebene Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktion.

> [!NOTE]
> Sie bieten auch eigene `KeyVaultClient` Implementierung `AddAzureKeyVault`. Eine benutzerdefinierte Clienteinstellung ermöglicht, eine einzelne Instanz des Clients für die app freigegeben wird.

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a>Authentifizieren Sie mit Azure Key Vault mit einem x. 509-Zertifikat

Wenn Sie eine .NET Framework-app in einer Umgebung entwickeln, die Zertifikate unterstützt, können Sie mit einem x. 509-Zertifikat in Azure Key Vault authentifizieren. Privaten Schlüssel des x. 509-Zertifikats wird vom Betriebssystem verwaltet. Weitere Informationen finden Sie unter [authentifizieren mit einem Zertifikat anstelle eines geheimen Clientschlüssels](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret). Verwenden der `AddAzureKeyVault` Überladung verwenden, akzeptiert eine `X509Certificate2` (`_env` im folgenden Beispiel:

```csharp
var builtConfig = config.Build();

var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates
    .Find(X509FindType.FindByThumbprint, 
        config["CertificateThumbprint"], false);

config.AddAzureKeyVault(
    builtConfig["Vault"],
    builtConfig["ClientId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(context.HostingEnvironment.ApplicationName));

store.Close();
```

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

Der Anbieter ist der Konfigurationswerte in ein Array für die Bindung an ein POCO-Array lesen kann.

Wenn aus einer Konfigurationsquelle zu lesen, die Schlüssel für den Doppelpunkt enthalten kann (`:`) Trennzeichen, die einen numerischen Schlüsselsegment wird verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array (`:0:`, `:1:`,... `:{n}:`) angezeigt wird. Weitere Informationen finden Sie unter [Konfiguration: Ein Array an eine Klasse gebunden](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwendet. In diesem Thema beschriebene Ansatz verwendet die doppelten Bindestriche (`--`) als Trennzeichen bei hierarchischen Werten (Abschnitte). Array-Schlüssel werden in Azure Key Vault gespeichert, mit doppelten Bindestrichen und numerische wichtigsten Segmente (`--0--`, `--1--`,... `--{n}--`) angezeigt wird.

Überprüfen Sie die folgenden [Serilog](https://serilog.net/) Protokollierung Anbieterkonfiguration, die von einer JSON-Datei bereitgestellt. Es gibt zwei Literale in definierte Objekt der `WriteTo` Array, das zwei Serilog entsprechend *senken*, welche Ziele für die Ausgabe der konsolenprotokollierung beschreiben:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

Die Konfiguration dargestellt, in der obigen JSON-Datei befindet sich in Azure Key Vault mit doppelter Bindestrich (`--`)-Notation und numerische Segmente:

| Key | Wert |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Laden Sie die Geheimnisse

Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird. Abgelaufen ist, deaktiviert, und die aktualisierte Geheimnisse im schlüsseltresor werden nicht berücksichtigt, von der app bis `Reload` ausgeführt wird.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Deaktiviert und abgelaufene Geheimnisse

Deaktiviert und abgelaufene Geheimnisse Auslösen einer `KeyVaultClientException`. Um zu verhindern, dass Ihre app auslösen, ersetzen Sie Ihre app, oder aktualisieren Sie das Geheimnis deaktiviert/ist abgelaufen.

## <a name="troubleshoot"></a>Problembehandlung

Bei die app ein Fehler beim Laden der Konfiguration, die mithilfe des Anbieters auftritt, wird eine Fehlermeldung geschrieben, auf die [Infrastruktur von ASP.NET Core-Protokollierung](xref:fundamentals/logging/index). Die folgenden Bedingungen werden verhindert, dass die Konfiguration geladen:

* Die app ist nicht ordnungsgemäß in Azure Active Directory konfiguriert.
* Die Key Vault-Instanz ist in Azure Key Vault nicht vorhanden.
* Die app ist nicht autorisiert, auf den schlüsseltresor zugreifen.
* Die Zugriffsrichtlinie beinhaltet keine `Get` und `List` Berechtigungen.
* In den schlüsseltresor ist die Konfigurationsdaten (Name / Wert-Paar) falsch benannt, fehlen, deaktiviert oder abgelaufen.
* Die app hat den falschen schlüsseltresornamen (`Vault`), Azure AD-App-Id (`ClientId`), oder Azure AD Key (`ClientSecret`).
* Die Azure AD Key (`ClientSecret`) ist abgelaufen.
* Der Konfigurationsschlüssel (Name) ist falsch, in der app für den Wert an, die, den Sie laden möchten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault-Instanz](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Dokumentation zu Key Vault](/azure/key-vault/)
* [Das Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient-Klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
