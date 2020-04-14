---
title: Azure Key Vault-Konfigurationsanbieter in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie den Azure Key Vault-Konfigurationsanbieter verwenden, um eine App mit Namen-Wert-Paaren zu konfigurieren, die zur Laufzeit geladen werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228165"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault-Konfigurationsanbieter in ASP.NET Core

Von [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault-Konfigurationsanbieter](https://azure.microsoft.com/services/key-vault/) verwenden, um App-Konfigurationswerte aus Den Geheimnissen von Azure Key Vault zu laden. Azure Key Vault ist ein cloudbasierter Dienst, der beim Schutz kryptografischer Schlüssel und Geheimnisse von Apps und Diensten unterstützt wird. Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-Apps sind:

* Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.
* Erfüllung der Anforderung für FIPS 140-2 Level 2 validierte Hardware-Sicherheitsmodule (HSM) beim Speichern von Konfigurationsdaten.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([downloaden](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakete

Fügen Sie dem [Microsoft.Extensions.Configuration.AzureKeyVault-Paket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) einen Paketverweis hinzu.

## <a name="sample-app"></a>Beispiel-App

Die Beispiel-App wird in einem der `#define` beiden Modi ausgeführt, die durch die Anweisung oben in der *Program.cs-Datei* bestimmt werden:

* `Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X.509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse. Diese Version des Beispiels kann von jedem Speicherort aus ausgeführt, in Azure App Service oder auf jedem Host bereitgestellt werden, der eine ASP.NET Core-App bereitgestellt werden kann.
* `Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die App bei Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen zu authentifizieren, die im Code oder in der Konfiguration der App gespeichert sind. Wenn verwaltete Identitäten zur Authentifizierung verwendet werden, sind keine Azure AD-Anwendungs-ID und ein Kennwort (Client Secret) erforderlich. Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden. Befolgen Sie die Anleitung im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen.](#use-managed-identities-for-azure-resources)

Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe`#define`von <xref:index#preprocessor-directives-in-sample-code>Präprozessordirektiven ( ) finden Sie unter .

## <a name="secret-storage-in-the-development-environment"></a>Geheime Speicherung in der Entwicklungsumgebung

Legen Sie Geheimnisse lokal mit dem [Secret Manager-Tool](xref:security/app-secrets)fest. Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Secret Manager-Speicher geladen.

Das Tool "Geheimer Manager" erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der App. Legen Sie den`{GUID}`Eigenschaftswert ( ) auf eine beliebige eindeutige GUID fest:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Geheimnisse werden als Name-Wert-Paare erstellt. Hierarchische Werte (Konfigurationsabschnitte) `:` verwenden ein (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfigurationsschlüsselnamen.](xref:fundamentals/configuration/index)

Der geheime Manager wird von einer Befehlsshell verwendet, `{SECRET NAME}` die zum `{SECRET VALUE}` [Inhaltsstamm](xref:fundamentals/index#content-root)des Projekts geöffnet wird, wobei der Name und der Wert der Folgende ist:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhaltsstamm](xref:fundamentals/index#content-root) des Projekts aus, um die Geheimnisse für die Beispiel-App festzulegen:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in `_prod`geändert. Das Suffix bietet einen visuellen Hinweis in der Ausgabe der App, der die Quelle der Konfigurationswerte angibt.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Geheimspeicher in der Produktionsumgebung mit Azure Key Vault

Die Anweisungen des [Schnellstarts: Festlegen und Abrufen eines geheimen Schlüssels aus Azure Key Vault mithilfe des Azure](/azure/key-vault/quick-create-cli) CLI-Themas sind hier zusammengefasst, um einen Azure Key Vault zu erstellen und geheime Informationen zu speichern, die von der Beispiel-App verwendet werden. Weitere Informationen finden Sie im Thema.

1. Öffnen Sie die Azure Cloud-Shell mit einer der folgenden Methoden im [Azure-Portal:](https://portal.azure.com/)

   * Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Verwenden Sie die Suchzeichenfolge "Azure CLI" im Textfeld.
   * Öffnen Sie Cloud Shell in Ihrem Browser mit der **Schaltfläche Cloud Shell starten.**
   * Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.

   Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und Übersicht über Azure [Cloud Shell](/azure/cloud-shell/overview).

1. Wenn Sie noch nicht authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.

1. Erstellen Sie eine Ressourcengruppe mit `{RESOURCE GROUP NAME}` dem folgenden Befehl, wobei der `{LOCATION}` Name der Ressourcengruppe für die neue Ressourcengruppe und die Azure-Region (Datencenter) lautet:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie einen Schlüsseltresor in der Ressourcengruppe mit dem folgenden Befehl, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüsseltresor und `{LOCATION}` die Azure-Region (Datencenter) lautet:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie Geheimnisse im Schlüsseltresor als Name-Wert-Paare.

   Geheime Namen von Azure Key Vault sind auf alphanumerische Zeichen und Bindestriche beschränkt. Hierarchische Werte (Konfigurationsabschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen. Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index)zu trennen, sind in geheimen Schlüsseltresornamen nicht zulässig. Daher werden zwei Bindestriche verwendet und gegen einen Doppelpunkt getauscht, wenn die Geheimnisse in die Konfiguration der App geladen werden.

   Die folgenden Geheimnisse sind für die Verwendung mit der Beispiel-App vorgesehen. Die Werte `_prod` enthalten ein Suffix, `_dev` um sie von den in der Entwicklungsumgebung geladenen Suffixwerten von Benutzergeheimnissen zu unterscheiden. Ersetzen `{KEY VAULT NAME}` Sie dies durch den Namen des Schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Verwenden von Anwendungs-ID und X.509-Zertifikat für nicht von Azure gehostete Apps

Konfigurieren Sie Azure AD, Azure Key Vault und die App so, dass sie eine Azure Active Directory-Anwendungs-ID und ein X.509-Zertifikat verwenden, um sich bei einem Schlüsseltresor zu authentifizieren, **wenn die App außerhalb von Azure gehostet wird.** Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Obwohl die Verwendung einer Anwendungs-ID und eines X.509-Zertifikats für in Azure gehostete Apps unterstützt wird, wird empfohlen, [verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer App in Azure zu verwenden. Verwaltete Identitäten erfordern nicht das Speichern eines Zertifikats in der App oder in der Entwicklungsumgebung.

Die Beispiel-App verwendet eine Anwendungs-ID und `#define` ein X.509-Zertifikat, `Certificate`wenn die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.

1. Erstellen Sie ein PKCS-12-Archiv (*.pfx*) Zertifikat. Zu den Optionen zum Erstellen von Zertifikaten gehören [MakeCert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).
1. Installieren Sie das Zertifikat im persönlichen Zertifikatspeicher des aktuellen Benutzers. Die Markierung des Schlüssels als exportierbar ist optional. Beachten Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.
1. Exportieren Sie das PKCS-12-Archiv (*.pfx*) zertifikat als DER-codiertes Zertifikat (*.cer*).
1. Registrieren Sie die App bei Azure AD (**App-Registrierungen**).
1. Laden Sie das DER-codierte Zertifikat (*.cer*) in Azure AD hoch:
   1. Wählen Sie die App in Azure AD aus.
   1. Navigieren Sie zu **Zertifikate & Geheimnissen**.
   1. Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält. Ein *.cer*-, *.pem*- oder *.crt-Zertifikat* ist zulässig.
1. Speichern Sie den Schlüsseltresornamen, die Anwendungs-ID und den Zertifikatfingerabdruck in der *App-Datei appsettings.json.*
1. Navigieren Sie zu **Schlüsseltresoren** im Azure-Portal.
1. Wählen Sie den Schlüsseltresor aus, den Sie im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) erstellt haben.
1. Klicken Sie auf **Zugriffsrichtlinien**.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen**aus.
1. Öffnen Sie **geheime Berechtigungen,** und stellen Sie der App **Berechtigung zum Abrufen** und **Auflisten** zur Verfügung.
1. Wählen Sie **Prinzipal auswählen** aus, und wählen Sie die registrierte App nach Namen aus. Wählen Sie die Schaltfläche **Auswählen** aus.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.
1. Stellen Sie die App bereit.

Die `Certificate` Beispiel-App ruft ihre `IConfigurationRoot` Konfigurationswerte mit demselben Namen wie der geheime Name ab:

* Nicht hierarchische Werte: Der `SecretName` Wert `config["SecretName"]`für wird mit abgerufen.
* Hierarchische Werte (Abschnitte): `:` Verwenden Sie die `GetSection` Notation (Kolon) oder die Erweiterungsmethode. Verwenden Sie einen der folgenden Ansätze, um den Konfigurationswert zu erhalten:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Das X.509-Zertifikat wird vom Betriebssystem verwaltet. Die App <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ruft Mit Werten auf, die von der Datei *appsettings.json* bereitgestellt werden:

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Beispielwerte:

* Name des Schlüsseltresors:`contosovault`
* Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* Zertifikat-Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung werden geheime `_dev` Werte mit dem Suffix geladen. In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen.

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

**Eine in Azure bereitgestellte App** kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, wodurch sich die App mit Azure Key Vault mithilfe der Azure AD-Authentifizierung ohne in der App gespeicherte Anmeldeinformationen (Anwendungs-ID und Kennwort/geheime sende Client) authentifizieren kann.

Die Beispiel-App verwendet verwaltete `#define` Identitäten für Azure-Ressourcen, wenn `Managed`die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.

Geben Sie den Tresornamen in die *Appappappsettings.json-Datei* ein. Die Beispiel-App benötigt keine Anwendungs-ID und kein Kennwort `Managed` (Client Secret), wenn sie auf die Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können. Die App wird in Azure bereitgestellt, und Azure authentifiziert die App für den Zugriff auf Azure Key Vault nur unter Verwendung des Tresornamens, der in der Datei *appsettings.json* gespeichert ist.

Stellen Sie die Beispiel-App in Azure App Service bereit.

Eine in Azure App Service bereitgestellte App wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird. Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung im folgenden Befehl ab. Die Objekt-ID wird im Azure-Portal im **Identitätsbedienfeld** des App-Dienstes angezeigt.

Stellen Sie der App mithilfe der Azure CLI `list` `get` und der Objekt-ID der App Berechtigungen für den Zugriff auf den Schlüsseltresor zur Verfügung:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Starten Sie die App** mit Azure CLI, PowerShell oder dem Azure-Portal neu.

Die Beispiel-App:

* Erstellt eine Instanz `AzureServiceTokenProvider` der Klasse ohne Verbindungszeichenfolge. Wenn keine Verbindungszeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen abzurufen.
* Mit <xref:Microsoft.Azure.KeyVault.KeyVaultClient> dem `AzureServiceTokenProvider` Instance-Tokenrückruf wird ein neues erstellt.
* Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Standardimplementierung verwendet, die alle geheimen`--`Werte lädt und`:`Doppelstriche ( ) durch Doppelpunkte ( ) in Schlüsselnamen ersetzt.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Beispielwert für den Schlüsseltresornamen:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung haben `_dev` geheime Werte das Suffix, da sie von Benutzergeheimnissen bereitgestellt werden. In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen, da sie von Azure Key Vault bereitgestellt werden.

Wenn sie `Access denied` eine Fehlermeldung erhalten, vergewissern Sie sich, dass die App bei Azure AD registriert ist und Zugriff auf den Schlüsseltresor gewährt wurde. Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.

Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure DevOps-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager-Dienstverbindung zu einer VM mit einer verwalteten Dienstidentität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Konfigurationsoptionen

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>kann eine <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>akzeptieren:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Eigenschaft         | BESCHREIBUNG |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>zum Abrufen von Werten zu verwenden. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Instanz, die verwendet wird, um das geheime Laden zu steuern. |
| `ReloadInterval` | `Timespan`zwischen den Versuchen zu warten, den Schlüsseltresor nach Änderungen abzufragen. Der Standardwert `null` ist (Konfiguration wird nicht neu geladen). |
| `Vault`          | Schlüsseltresor-URI. |

## <a name="use-a-key-name-prefix"></a>Verwenden eines Schlüsselnamenspräfixes

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bietet eine Überladung, die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>eine Implementierung von akzeptiert, mit der Sie steuern können, wie Schlüsseltresorgeheimnisse in Konfigurationsschlüssel konvertiert werden. Sie können die Schnittstelle beispielsweise implementieren, um geheime Werte basierend auf einem Präfixwert zu laden, den Sie beim App-Start angeben. Auf diese Weise können Sie z. B. Geheimnisse basierend auf der Version der App laden.

> [!WARNING]
> Verwenden Sie keine Präfixe für wichtige Tresorgeheimnisse, um Geheimnisse für mehrere Apps in demselben Schlüsseltresor zu platzieren oder Umweltgeheimnisse (z. B. *Entwicklungs-* und *Produktionsgeheimnisse)* in demselben Tresor zu platzieren. Es wird empfohlen, dass verschiedene Apps und Entwicklungs-/Produktionsumgebungen separate Schlüsseltresore verwenden, um App-Umgebungen für ein Höchstmaß an Sicherheit zu isolieren.

Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüsseltresor (und mit dem `5000-AppSecret` Geheimen Manager-Tool für die Entwicklungsumgebung) für (Zeiträume sind in geheimen Schlüsseltresornamen nicht zulässig) eingerichtet. Dieser Geheime stellt einen Geheimschlüssel für die Version 5.0.0.0 der App dar. Für eine andere Version der App, 5.1.0.0, wird dem Schlüsseltresor (und `5100-AppSecret`der Verwendung des Geheimen Managers) für ein geheimer Schlüssel hinzugefügt. Jede App-Version lädt ihren versionierten `AppSecret`geheimen Wert in ihre Konfiguration als , um die Version zu entfernen, während sie den geheimen Wert lädt.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>benutzerdefinierten aufgerufen:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versionspräfixe von Geheimnissen, um den richtigen Geheimschlüssel in die Konfiguration zu laden:

* `Load`lädt einen geheimen Schlüssel, wenn der Name mit dem Präfix beginnt. Andere Geheimnisse werden nicht geladen.
* `GetKey`:
  * Entfernt das Präfix aus dem geheimen Namen.
  * Ersetzt zwei Bindestriche in `KeyDelimiter`einem beliebigen Namen durch die , die das Trennzeichen ist, das in der Konfiguration verwendet wird (in der Regel ein Doppelpunkt). Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Die `Load` Methode wird von einem Anbieteralgorithmus aufgerufen, der durch die Tresorgeheimnisse iteriert, um diejenigen zu finden, die das Versionspräfix haben. Wenn ein Versionspräfix `Load`mit gefunden wird, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des geheimen Namens zurückzugeben. Das Versionspräfix wird vom Namen des Geheimen entfernt und der Rest des geheimen Namens zum Laden in die Konfigurationsnamen-Wert-Paare der App zurückgegeben.

Wenn dieser Ansatz implementiert wird:

1. Die in der Projektdatei der App angegebene App-Version. Im folgenden Beispiel ist die App-Version `5.0.0.0`auf:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vergewissern `<UserSecretsId>` Sie sich, dass eine Eigenschaft `{GUID}` in der Projektdatei der App vorhanden ist, wobei sich eine vom Benutzer bereitgestellte GUID befindet:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Speichern Sie die folgenden Geheimnisse lokal mit dem [Secret Manager-Tool:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Geheimnisse werden in Azure Key Vault mit den folgenden Azure CLI-Befehlen gespeichert:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Wenn die App ausgeführt wird, werden die Schlüsseltresorgeheimnisse geladen. Der geheime `5000-AppSecret` Zeichenfolgenschlüssel für wird mit der in der Projektdatei der App angegebenen App-Version (`5.0.0.0`) abgeglichen.

1. Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt. In der gesamten App wird `AppSecret` der geheime Wert in der gesamten App beim Lesen der Konfiguration mit dem Schlüssel geladen.

1. Wenn die Version der App in der `5.1.0.0` Projektdatei geändert wird und die `5.1.0.0_secret_value_dev` App erneut ausgeführt `5.1.0.0_secret_value_prod` wird, wird der zurückgegebene geheime Wert in der Entwicklungsumgebung und in der Produktion zurückgegeben.

> [!NOTE]
> Sie können auch <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Ihre <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>eigene Implementierung für bereitstellen. Ein benutzerdefinierter Client ermöglicht die Freigabe einer einzelnen Instanz des Clients in der App.

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

Der Anbieter ist in der Lage, Konfigurationswerte in ein Array zur Bindung an ein POCO-Array zu lesen.

Beim Lesen aus einer Konfigurationsquelle, die`:`es Tasten ermöglicht, Doppelpunkte ( ) Trennzeichen zu enthalten, `:1:` &hellip; `:{n}:`wird ein numerisches Schlüsselsegment verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array besteht ( ,`:0:`, ). Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden. Der in diesem Thema beschriebene Ansatz`--`verwendet doppelte Bindestriche ( ) als Trennzeichen für hierarchische Werte (Abschnitte). Arrayschlüssel`--0--`werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( , `--1--`, &hellip; `--{n}--`) gespeichert.

Untersuchen Sie die folgende [Serilog-Protokollierungsanbieterkonfiguration,](https://serilog.net/) die von einer JSON-Datei bereitgestellt wird. Es sind zwei Objektliterale `WriteTo` im Array definiert, die zwei Serilog-Senken widerspiegeln, die Ziele für die Protokollierungsausgabe beschreiben: *sinks*

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

Die in der vorherigen JSON-Datei gezeigte Konfiguration wird`--`in Azure Key Vault mit doppelter Bindestrich - ) Notation und numerischen Segmenten gespeichert:

| Schlüssel | Wert |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Geheimnisse neu laden

Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` sie aufgerufen werden. Abgelaufene, deaktivierte und aktualisierte Geheimnisse im Schlüsseltresor `Reload` werden von der App erst respektiert, wenn sie ausgeführt wird.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Deaktivierte und abgelaufene Geheimnisse

Deaktivierte und abgelaufene Geheimnisse werfen eine <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>aus. Um zu verhindern, dass die App ausgeworfen wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurationsanbieters bereit, oder aktualisieren Sie den deaktivierten oder abgelaufenen geheimen Schlüssel.

## <a name="troubleshoot"></a>Problembehandlung

Wenn die App die Konfiguration nicht über den Anbieter lädt, wird eine Fehlermeldung in die [ASP.NET Core Logging Infrastructure](xref:fundamentals/logging/index)geschrieben. Die folgenden Bedingungen verhindern das Laden der Konfiguration:

* Die App oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.
* Der Schlüsseltresor ist in Azure Key Vault nicht vorhanden.
* Die App ist nicht berechtigt, auf den Schlüsseltresor zuzugreifen.
* Die Zugriffsrichtlinie enthält `Get` keine `List` Berechtigungen.
* Im Schlüsseltresor werden die Konfigurationsdaten (Name-Wert-Paar) fälschlicherweise benannt, fehlen, deaktiviert oder abgelaufen.
* Die App hat den falschen`KeyVaultName`Schlüsseltresornamen (`AzureADApplicationId`), Azure AD Application`AzureADCertThumbprint`Id ( ), oder Azure AD-Zertifikatfingerabdruck ( ).
* Der Konfigurationsschlüssel (Name) ist in der App für den Wert, den Sie laden möchten, falsch.
* Beim Hinzufügen der Zugriffsrichtlinie für die App zum Schlüsseltresor wurde die Richtlinie erstellt, die Schaltfläche **Speichern** wurde jedoch nicht in der Benutzeroberfläche **für Zugriffsrichtlinien** ausgewählt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Schlüsseltresor](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Wichtige Vault-Dokumentation](/azure/key-vault/)
* [Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient-Klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App](/azure/key-vault/quick-create-net)
* [Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault-Konfigurationsanbieter](https://azure.microsoft.com/services/key-vault/) verwenden, um App-Konfigurationswerte aus Den Geheimnissen von Azure Key Vault zu laden. Azure Key Vault ist ein cloudbasierter Dienst, der beim Schutz kryptografischer Schlüssel und Geheimnisse von Apps und Diensten unterstützt wird. Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-Apps sind:

* Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.
* Erfüllung der Anforderung für FIPS 140-2 Level 2 validierte Hardware-Sicherheitsmodule (HSM) beim Speichern von Konfigurationsdaten.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([downloaden](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakete

Fügen Sie dem [Microsoft.Extensions.Configuration.AzureKeyVault-Paket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) einen Paketverweis hinzu.

## <a name="sample-app"></a>Beispiel-App

Die Beispiel-App wird in einem der `#define` beiden Modi ausgeführt, die durch die Anweisung oben in der *Program.cs-Datei* bestimmt werden:

* `Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X.509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse. Diese Version des Beispiels kann von jedem Speicherort aus ausgeführt, in Azure App Service oder auf jedem Host bereitgestellt werden, der eine ASP.NET Core-App bereitgestellt werden kann.
* `Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die App bei Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen zu authentifizieren, die im Code oder in der Konfiguration der App gespeichert sind. Wenn verwaltete Identitäten zur Authentifizierung verwendet werden, sind keine Azure AD-Anwendungs-ID und ein Kennwort (Client Secret) erforderlich. Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden. Befolgen Sie die Anleitung im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen.](#use-managed-identities-for-azure-resources)

Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe`#define`von <xref:index#preprocessor-directives-in-sample-code>Präprozessordirektiven ( ) finden Sie unter .

## <a name="secret-storage-in-the-development-environment"></a>Geheime Speicherung in der Entwicklungsumgebung

Legen Sie Geheimnisse lokal mit dem [Secret Manager-Tool](xref:security/app-secrets)fest. Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Secret Manager-Speicher geladen.

Das Tool "Geheimer Manager" erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der App. Legen Sie den`{GUID}`Eigenschaftswert ( ) auf eine beliebige eindeutige GUID fest:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Geheimnisse werden als Name-Wert-Paare erstellt. Hierarchische Werte (Konfigurationsabschnitte) `:` verwenden ein (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfigurationsschlüsselnamen.](xref:fundamentals/configuration/index)

Der geheime Manager wird von einer Befehlsshell verwendet, `{SECRET NAME}` die zum `{SECRET VALUE}` [Inhaltsstamm](xref:fundamentals/index#content-root)des Projekts geöffnet wird, wobei der Name und der Wert der Folgende ist:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhaltsstamm](xref:fundamentals/index#content-root) des Projekts aus, um die Geheimnisse für die Beispiel-App festzulegen:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in `_prod`geändert. Das Suffix bietet einen visuellen Hinweis in der Ausgabe der App, der die Quelle der Konfigurationswerte angibt.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Geheimspeicher in der Produktionsumgebung mit Azure Key Vault

Die Anweisungen des [Schnellstarts: Festlegen und Abrufen eines geheimen Schlüssels aus Azure Key Vault mithilfe des Azure](/azure/key-vault/quick-create-cli) CLI-Themas sind hier zusammengefasst, um einen Azure Key Vault zu erstellen und geheime Informationen zu speichern, die von der Beispiel-App verwendet werden. Weitere Informationen finden Sie im Thema.

1. Öffnen Sie die Azure Cloud-Shell mit einer der folgenden Methoden im [Azure-Portal:](https://portal.azure.com/)

   * Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Verwenden Sie die Suchzeichenfolge "Azure CLI" im Textfeld.
   * Öffnen Sie Cloud Shell in Ihrem Browser mit der **Schaltfläche Cloud Shell starten.**
   * Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.

   Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und Übersicht über Azure [Cloud Shell](/azure/cloud-shell/overview).

1. Wenn Sie noch nicht authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.

1. Erstellen Sie eine Ressourcengruppe mit `{RESOURCE GROUP NAME}` dem folgenden Befehl, wobei der `{LOCATION}` Name der Ressourcengruppe für die neue Ressourcengruppe und die Azure-Region (Datencenter) lautet:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie einen Schlüsseltresor in der Ressourcengruppe mit dem folgenden Befehl, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüsseltresor und `{LOCATION}` die Azure-Region (Datencenter) lautet:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie Geheimnisse im Schlüsseltresor als Name-Wert-Paare.

   Geheime Namen von Azure Key Vault sind auf alphanumerische Zeichen und Bindestriche beschränkt. Hierarchische Werte (Konfigurationsabschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen. Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index)zu trennen, sind in geheimen Schlüsseltresornamen nicht zulässig. Daher werden zwei Bindestriche verwendet und gegen einen Doppelpunkt getauscht, wenn die Geheimnisse in die Konfiguration der App geladen werden.

   Die folgenden Geheimnisse sind für die Verwendung mit der Beispiel-App vorgesehen. Die Werte `_prod` enthalten ein Suffix, `_dev` um sie von den in der Entwicklungsumgebung geladenen Suffixwerten von Benutzergeheimnissen zu unterscheiden. Ersetzen `{KEY VAULT NAME}` Sie dies durch den Namen des Schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Verwenden von Anwendungs-ID und X.509-Zertifikat für nicht von Azure gehostete Apps

Konfigurieren Sie Azure AD, Azure Key Vault und die App so, dass sie eine Azure Active Directory-Anwendungs-ID und ein X.509-Zertifikat verwenden, um sich bei einem Schlüsseltresor zu authentifizieren, **wenn die App außerhalb von Azure gehostet wird.** Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Obwohl die Verwendung einer Anwendungs-ID und eines X.509-Zertifikats für in Azure gehostete Apps unterstützt wird, wird empfohlen, [verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer App in Azure zu verwenden. Verwaltete Identitäten erfordern nicht das Speichern eines Zertifikats in der App oder in der Entwicklungsumgebung.

Die Beispiel-App verwendet eine Anwendungs-ID und `#define` ein X.509-Zertifikat, `Certificate`wenn die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.

1. Erstellen Sie ein PKCS-12-Archiv (*.pfx*) Zertifikat. Zu den Optionen zum Erstellen von Zertifikaten gehören [MakeCert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).
1. Installieren Sie das Zertifikat im persönlichen Zertifikatspeicher des aktuellen Benutzers. Die Markierung des Schlüssels als exportierbar ist optional. Beachten Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.
1. Exportieren Sie das PKCS-12-Archiv (*.pfx*) zertifikat als DER-codiertes Zertifikat (*.cer*).
1. Registrieren Sie die App bei Azure AD (**App-Registrierungen**).
1. Laden Sie das DER-codierte Zertifikat (*.cer*) in Azure AD hoch:
   1. Wählen Sie die App in Azure AD aus.
   1. Navigieren Sie zu **Zertifikate & Geheimnissen**.
   1. Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält. Ein *.cer*-, *.pem*- oder *.crt-Zertifikat* ist zulässig.
1. Speichern Sie den Schlüsseltresornamen, die Anwendungs-ID und den Zertifikatfingerabdruck in der *App-Datei appsettings.json.*
1. Navigieren Sie zu **Schlüsseltresoren** im Azure-Portal.
1. Wählen Sie den Schlüsseltresor aus, den Sie im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) erstellt haben.
1. Klicken Sie auf **Zugriffsrichtlinien**.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen**aus.
1. Öffnen Sie **geheime Berechtigungen,** und stellen Sie der App **Berechtigung zum Abrufen** und **Auflisten** zur Verfügung.
1. Wählen Sie **Prinzipal auswählen** aus, und wählen Sie die registrierte App nach Namen aus. Wählen Sie die Schaltfläche **Auswählen** aus.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.
1. Stellen Sie die App bereit.

Die `Certificate` Beispiel-App ruft ihre `IConfigurationRoot` Konfigurationswerte mit demselben Namen wie der geheime Name ab:

* Nicht hierarchische Werte: Der `SecretName` Wert `config["SecretName"]`für wird mit abgerufen.
* Hierarchische Werte (Abschnitte): `:` Verwenden Sie die `GetSection` Notation (Kolon) oder die Erweiterungsmethode. Verwenden Sie einen der folgenden Ansätze, um den Konfigurationswert zu erhalten:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Das X.509-Zertifikat wird vom Betriebssystem verwaltet. Die App <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ruft Mit Werten auf, die von der Datei *appsettings.json* bereitgestellt werden:

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Beispielwerte:

* Name des Schlüsseltresors:`contosovault`
* Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* Zertifikat-Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung werden geheime `_dev` Werte mit dem Suffix geladen. In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen.

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

**Eine in Azure bereitgestellte App** kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, wodurch sich die App mit Azure Key Vault mithilfe der Azure AD-Authentifizierung ohne in der App gespeicherte Anmeldeinformationen (Anwendungs-ID und Kennwort/geheime sende Client) authentifizieren kann.

Die Beispiel-App verwendet verwaltete `#define` Identitäten für Azure-Ressourcen, wenn `Managed`die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.

Geben Sie den Tresornamen in die *Appappappsettings.json-Datei* ein. Die Beispiel-App benötigt keine Anwendungs-ID und kein Kennwort `Managed` (Client Secret), wenn sie auf die Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können. Die App wird in Azure bereitgestellt, und Azure authentifiziert die App für den Zugriff auf Azure Key Vault nur unter Verwendung des Tresornamens, der in der Datei *appsettings.json* gespeichert ist.

Stellen Sie die Beispiel-App in Azure App Service bereit.

Eine in Azure App Service bereitgestellte App wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird. Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung im folgenden Befehl ab. Die Objekt-ID wird im Azure-Portal im **Identitätsbedienfeld** des App-Dienstes angezeigt.

Stellen Sie der App mithilfe der Azure CLI `list` `get` und der Objekt-ID der App Berechtigungen für den Zugriff auf den Schlüsseltresor zur Verfügung:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Starten Sie die App** mit Azure CLI, PowerShell oder dem Azure-Portal neu.

Die Beispiel-App:

* Erstellt eine Instanz `AzureServiceTokenProvider` der Klasse ohne Verbindungszeichenfolge. Wenn keine Verbindungszeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen abzurufen.
* Mit <xref:Microsoft.Azure.KeyVault.KeyVaultClient> dem `AzureServiceTokenProvider` Instance-Tokenrückruf wird ein neues erstellt.
* Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Standardimplementierung verwendet, die alle geheimen`--`Werte lädt und`:`Doppelstriche ( ) durch Doppelpunkte ( ) in Schlüsselnamen ersetzt.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Beispielwert für den Schlüsseltresornamen:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung haben `_dev` geheime Werte das Suffix, da sie von Benutzergeheimnissen bereitgestellt werden. In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen, da sie von Azure Key Vault bereitgestellt werden.

Wenn sie `Access denied` eine Fehlermeldung erhalten, vergewissern Sie sich, dass die App bei Azure AD registriert ist und Zugriff auf den Schlüsseltresor gewährt wurde. Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.

Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure DevOps-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager-Dienstverbindung zu einer VM mit einer verwalteten Dienstidentität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Verwenden eines Schlüsselnamenspräfixes

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bietet eine Überladung, die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>eine Implementierung von akzeptiert, mit der Sie steuern können, wie Schlüsseltresorgeheimnisse in Konfigurationsschlüssel konvertiert werden. Sie können die Schnittstelle beispielsweise implementieren, um geheime Werte basierend auf einem Präfixwert zu laden, den Sie beim App-Start angeben. Auf diese Weise können Sie z. B. Geheimnisse basierend auf der Version der App laden.

> [!WARNING]
> Verwenden Sie keine Präfixe für wichtige Tresorgeheimnisse, um Geheimnisse für mehrere Apps in demselben Schlüsseltresor zu platzieren oder Umweltgeheimnisse (z. B. *Entwicklungs-* und *Produktionsgeheimnisse)* in demselben Tresor zu platzieren. Es wird empfohlen, dass verschiedene Apps und Entwicklungs-/Produktionsumgebungen separate Schlüsseltresore verwenden, um App-Umgebungen für ein Höchstmaß an Sicherheit zu isolieren.

Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüsseltresor (und mit dem `5000-AppSecret` Geheimen Manager-Tool für die Entwicklungsumgebung) für (Zeiträume sind in geheimen Schlüsseltresornamen nicht zulässig) eingerichtet. Dieser Geheime stellt einen Geheimschlüssel für die Version 5.0.0.0 der App dar. Für eine andere Version der App, 5.1.0.0, wird dem Schlüsseltresor (und `5100-AppSecret`der Verwendung des Geheimen Managers) für ein geheimer Schlüssel hinzugefügt. Jede App-Version lädt ihren versionierten `AppSecret`geheimen Wert in ihre Konfiguration als , um die Version zu entfernen, während sie den geheimen Wert lädt.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>benutzerdefinierten aufgerufen:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versionspräfixe von Geheimnissen, um den richtigen Geheimschlüssel in die Konfiguration zu laden:

* `Load`lädt einen geheimen Schlüssel, wenn der Name mit dem Präfix beginnt. Andere Geheimnisse werden nicht geladen.
* `GetKey`:
  * Entfernt das Präfix aus dem geheimen Namen.
  * Ersetzt zwei Bindestriche in `KeyDelimiter`einem beliebigen Namen durch die , die das Trennzeichen ist, das in der Konfiguration verwendet wird (in der Regel ein Doppelpunkt). Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Die `Load` Methode wird von einem Anbieteralgorithmus aufgerufen, der durch die Tresorgeheimnisse iteriert, um diejenigen zu finden, die das Versionspräfix haben. Wenn ein Versionspräfix `Load`mit gefunden wird, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des geheimen Namens zurückzugeben. Das Versionspräfix wird vom Namen des Geheimen entfernt und der Rest des geheimen Namens zum Laden in die Konfigurationsnamen-Wert-Paare der App zurückgegeben.

Wenn dieser Ansatz implementiert wird:

1. Die in der Projektdatei der App angegebene App-Version. Im folgenden Beispiel ist die App-Version `5.0.0.0`auf:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vergewissern `<UserSecretsId>` Sie sich, dass eine Eigenschaft `{GUID}` in der Projektdatei der App vorhanden ist, wobei sich eine vom Benutzer bereitgestellte GUID befindet:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Speichern Sie die folgenden Geheimnisse lokal mit dem [Secret Manager-Tool:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Geheimnisse werden in Azure Key Vault mit den folgenden Azure CLI-Befehlen gespeichert:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Wenn die App ausgeführt wird, werden die Schlüsseltresorgeheimnisse geladen. Der geheime `5000-AppSecret` Zeichenfolgenschlüssel für wird mit der in der Projektdatei der App angegebenen App-Version (`5.0.0.0`) abgeglichen.

1. Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt. In der gesamten App wird `AppSecret` der geheime Wert in der gesamten App beim Lesen der Konfiguration mit dem Schlüssel geladen.

1. Wenn die Version der App in der `5.1.0.0` Projektdatei geändert wird und die `5.1.0.0_secret_value_dev` App erneut ausgeführt `5.1.0.0_secret_value_prod` wird, wird der zurückgegebene geheime Wert in der Entwicklungsumgebung und in der Produktion zurückgegeben.

> [!NOTE]
> Sie können auch <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Ihre <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>eigene Implementierung für bereitstellen. Ein benutzerdefinierter Client ermöglicht die Freigabe einer einzelnen Instanz des Clients in der App.

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

Der Anbieter ist in der Lage, Konfigurationswerte in ein Array zur Bindung an ein POCO-Array zu lesen.

Beim Lesen aus einer Konfigurationsquelle, die`:`es Tasten ermöglicht, Doppelpunkte ( ) Trennzeichen zu enthalten, `:1:` &hellip; `:{n}:`wird ein numerisches Schlüsselsegment verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array besteht ( ,`:0:`, ). Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden. Der in diesem Thema beschriebene Ansatz`--`verwendet doppelte Bindestriche ( ) als Trennzeichen für hierarchische Werte (Abschnitte). Arrayschlüssel`--0--`werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( , `--1--`, &hellip; `--{n}--`) gespeichert.

Untersuchen Sie die folgende [Serilog-Protokollierungsanbieterkonfiguration,](https://serilog.net/) die von einer JSON-Datei bereitgestellt wird. Es sind zwei Objektliterale `WriteTo` im Array definiert, die zwei Serilog-Senken widerspiegeln, die Ziele für die Protokollierungsausgabe beschreiben: *sinks*

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

Die in der vorherigen JSON-Datei gezeigte Konfiguration wird`--`in Azure Key Vault mit doppelter Bindestrich - ) Notation und numerischen Segmenten gespeichert:

| Schlüssel | Wert |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Geheimnisse neu laden

Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` sie aufgerufen werden. Abgelaufene, deaktivierte und aktualisierte Geheimnisse im Schlüsseltresor `Reload` werden von der App erst respektiert, wenn sie ausgeführt wird.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Deaktivierte und abgelaufene Geheimnisse

Deaktivierte und abgelaufene Geheimnisse werfen eine <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>aus. Um zu verhindern, dass die App ausgeworfen wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurationsanbieters bereit, oder aktualisieren Sie den deaktivierten oder abgelaufenen geheimen Schlüssel.

## <a name="troubleshoot"></a>Problembehandlung

Wenn die App die Konfiguration nicht über den Anbieter lädt, wird eine Fehlermeldung in die [ASP.NET Core Logging Infrastructure](xref:fundamentals/logging/index)geschrieben. Die folgenden Bedingungen verhindern das Laden der Konfiguration:

* Die App oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.
* Der Schlüsseltresor ist in Azure Key Vault nicht vorhanden.
* Die App ist nicht berechtigt, auf den Schlüsseltresor zuzugreifen.
* Die Zugriffsrichtlinie enthält `Get` keine `List` Berechtigungen.
* Im Schlüsseltresor werden die Konfigurationsdaten (Name-Wert-Paar) fälschlicherweise benannt, fehlen, deaktiviert oder abgelaufen.
* Die App hat den falschen`KeyVaultName`Schlüsseltresornamen (`AzureADApplicationId`), Azure AD Application`AzureADCertThumbprint`Id ( ), oder Azure AD-Zertifikatfingerabdruck ( ).
* Der Konfigurationsschlüssel (Name) ist in der App für den Wert, den Sie laden möchten, falsch.
* Beim Hinzufügen der Zugriffsrichtlinie für die App zum Schlüsseltresor wurde die Richtlinie erstellt, die Schaltfläche **Speichern** wurde jedoch nicht in der Benutzeroberfläche **für Zugriffsrichtlinien** ausgewählt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Schlüsseltresor](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Wichtige Vault-Dokumentation](/azure/key-vault/)
* [Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient-Klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App](/azure/key-vault/quick-create-net)
* [Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

