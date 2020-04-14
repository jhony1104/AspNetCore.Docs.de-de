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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="5657a-103">Azure Key Vault-Konfigurationsanbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5657a-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="5657a-104">Von [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5657a-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5657a-105">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault-Konfigurationsanbieter](https://azure.microsoft.com/services/key-vault/) verwenden, um App-Konfigurationswerte aus Den Geheimnissen von Azure Key Vault zu laden.</span><span class="sxs-lookup"><span data-stu-id="5657a-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="5657a-106">Azure Key Vault ist ein cloudbasierter Dienst, der beim Schutz kryptografischer Schlüssel und Geheimnisse von Apps und Diensten unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="5657a-107">Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-Apps sind:</span><span class="sxs-lookup"><span data-stu-id="5657a-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="5657a-108">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="5657a-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="5657a-109">Erfüllung der Anforderung für FIPS 140-2 Level 2 validierte Hardware-Sicherheitsmodule (HSM) beim Speichern von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="5657a-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="5657a-110">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5657a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="5657a-111">Pakete</span><span class="sxs-lookup"><span data-stu-id="5657a-111">Packages</span></span>

<span data-ttu-id="5657a-112">Fügen Sie dem [Microsoft.Extensions.Configuration.AzureKeyVault-Paket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="5657a-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5657a-113">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="5657a-113">Sample app</span></span>

<span data-ttu-id="5657a-114">Die Beispiel-App wird in einem der `#define` beiden Modi ausgeführt, die durch die Anweisung oben in der *Program.cs-Datei* bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="5657a-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="5657a-115">`Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X.509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="5657a-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="5657a-116">Diese Version des Beispiels kann von jedem Speicherort aus ausgeführt, in Azure App Service oder auf jedem Host bereitgestellt werden, der eine ASP.NET Core-App bereitgestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5657a-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="5657a-117">`Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die App bei Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen zu authentifizieren, die im Code oder in der Konfiguration der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="5657a-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="5657a-118">Wenn verwaltete Identitäten zur Authentifizierung verwendet werden, sind keine Azure AD-Anwendungs-ID und ein Kennwort (Client Secret) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5657a-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="5657a-119">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="5657a-120">Befolgen Sie die Anleitung im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="5657a-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="5657a-121">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe`#define`von <xref:index#preprocessor-directives-in-sample-code>Präprozessordirektiven ( ) finden Sie unter .</span><span class="sxs-lookup"><span data-stu-id="5657a-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="5657a-122">Geheime Speicherung in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="5657a-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="5657a-123">Legen Sie Geheimnisse lokal mit dem [Secret Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="5657a-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5657a-124">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Secret Manager-Speicher geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="5657a-125">Das Tool "Geheimer Manager" erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="5657a-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="5657a-126">Legen Sie den`{GUID}`Eigenschaftswert ( ) auf eine beliebige eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="5657a-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="5657a-127">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="5657a-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="5657a-128">Hierarchische Werte (Konfigurationsabschnitte) `:` verwenden ein (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfigurationsschlüsselnamen.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="5657a-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="5657a-129">Der geheime Manager wird von einer Befehlsshell verwendet, `{SECRET NAME}` die zum `{SECRET VALUE}` [Inhaltsstamm](xref:fundamentals/index#content-root)des Projekts geöffnet wird, wobei der Name und der Wert der Folgende ist:</span><span class="sxs-lookup"><span data-stu-id="5657a-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="5657a-130">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhaltsstamm](xref:fundamentals/index#content-root) des Projekts aus, um die Geheimnisse für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5657a-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="5657a-131">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in `_prod`geändert.</span><span class="sxs-lookup"><span data-stu-id="5657a-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="5657a-132">Das Suffix bietet einen visuellen Hinweis in der Ausgabe der App, der die Quelle der Konfigurationswerte angibt.</span><span class="sxs-lookup"><span data-stu-id="5657a-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="5657a-133">Geheimspeicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5657a-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="5657a-134">Die Anweisungen des [Schnellstarts: Festlegen und Abrufen eines geheimen Schlüssels aus Azure Key Vault mithilfe des Azure](/azure/key-vault/quick-create-cli) CLI-Themas sind hier zusammengefasst, um einen Azure Key Vault zu erstellen und geheime Informationen zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="5657a-135">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="5657a-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="5657a-136">Öffnen Sie die Azure Cloud-Shell mit einer der folgenden Methoden im [Azure-Portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="5657a-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="5657a-137">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="5657a-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="5657a-138">Verwenden Sie die Suchzeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="5657a-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="5657a-139">Öffnen Sie Cloud Shell in Ihrem Browser mit der **Schaltfläche Cloud Shell starten.**</span><span class="sxs-lookup"><span data-stu-id="5657a-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="5657a-140">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="5657a-141">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und Übersicht über Azure [Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="5657a-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="5657a-142">Wenn Sie noch nicht authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="5657a-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="5657a-143">Erstellen Sie eine Ressourcengruppe mit `{RESOURCE GROUP NAME}` dem folgenden Befehl, wobei der `{LOCATION}` Name der Ressourcengruppe für die neue Ressourcengruppe und die Azure-Region (Datencenter) lautet:</span><span class="sxs-lookup"><span data-stu-id="5657a-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5657a-144">Erstellen Sie einen Schlüsseltresor in der Ressourcengruppe mit dem folgenden Befehl, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüsseltresor und `{LOCATION}` die Azure-Region (Datencenter) lautet:</span><span class="sxs-lookup"><span data-stu-id="5657a-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5657a-145">Erstellen Sie Geheimnisse im Schlüsseltresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5657a-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="5657a-146">Geheime Namen von Azure Key Vault sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5657a-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="5657a-147">Hierarchische Werte (Konfigurationsabschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5657a-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5657a-148">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index)zu trennen, sind in geheimen Schlüsseltresornamen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="5657a-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="5657a-149">Daher werden zwei Bindestriche verwendet und gegen einen Doppelpunkt getauscht, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="5657a-150">Die folgenden Geheimnisse sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="5657a-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="5657a-151">Die Werte `_prod` enthalten ein Suffix, `_dev` um sie von den in der Entwicklungsumgebung geladenen Suffixwerten von Benutzergeheimnissen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="5657a-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="5657a-152">Ersetzen `{KEY VAULT NAME}` Sie dies durch den Namen des Schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="5657a-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="5657a-153">Verwenden von Anwendungs-ID und X.509-Zertifikat für nicht von Azure gehostete Apps</span><span class="sxs-lookup"><span data-stu-id="5657a-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="5657a-154">Konfigurieren Sie Azure AD, Azure Key Vault und die App so, dass sie eine Azure Active Directory-Anwendungs-ID und ein X.509-Zertifikat verwenden, um sich bei einem Schlüsseltresor zu authentifizieren, **wenn die App außerhalb von Azure gehostet wird.**</span><span class="sxs-lookup"><span data-stu-id="5657a-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="5657a-155">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="5657a-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="5657a-156">Obwohl die Verwendung einer Anwendungs-ID und eines X.509-Zertifikats für in Azure gehostete Apps unterstützt wird, wird empfohlen, [verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer App in Azure zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5657a-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="5657a-157">Verwaltete Identitäten erfordern nicht das Speichern eines Zertifikats in der App oder in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5657a-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="5657a-158">Die Beispiel-App verwendet eine Anwendungs-ID und `#define` ein X.509-Zertifikat, `Certificate`wenn die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="5657a-159">Erstellen Sie ein PKCS-12-Archiv (*.pfx*) Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="5657a-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="5657a-160">Zu den Optionen zum Erstellen von Zertifikaten gehören [MakeCert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="5657a-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="5657a-161">Installieren Sie das Zertifikat im persönlichen Zertifikatspeicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="5657a-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="5657a-162">Die Markierung des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="5657a-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="5657a-163">Beachten Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="5657a-164">Exportieren Sie das PKCS-12-Archiv (*.pfx*) zertifikat als DER-codiertes Zertifikat (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="5657a-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="5657a-165">Registrieren Sie die App bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="5657a-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="5657a-166">Laden Sie das DER-codierte Zertifikat (*.cer*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="5657a-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="5657a-167">Wählen Sie die App in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="5657a-168">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="5657a-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="5657a-169">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="5657a-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="5657a-170">Ein *.cer*-, *.pem*- oder *.crt-Zertifikat* ist zulässig.</span><span class="sxs-lookup"><span data-stu-id="5657a-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="5657a-171">Speichern Sie den Schlüsseltresornamen, die Anwendungs-ID und den Zertifikatfingerabdruck in der *App-Datei appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5657a-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="5657a-172">Navigieren Sie zu **Schlüsseltresoren** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5657a-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="5657a-173">Wählen Sie den Schlüsseltresor aus, den Sie im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="5657a-174">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="5657a-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="5657a-175">Wählen Sie **Zugriffsrichtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="5657a-176">Öffnen Sie **geheime Berechtigungen,** und stellen Sie der App **Berechtigung zum Abrufen** und **Auflisten** zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5657a-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="5657a-177">Wählen Sie **Prinzipal auswählen** aus, und wählen Sie die registrierte App nach Namen aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="5657a-178">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="5657a-179">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5657a-179">Select **OK**.</span></span>
1. <span data-ttu-id="5657a-180">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-180">Select **Save**.</span></span>
1. <span data-ttu-id="5657a-181">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="5657a-181">Deploy the app.</span></span>

<span data-ttu-id="5657a-182">Die `Certificate` Beispiel-App ruft ihre `IConfigurationRoot` Konfigurationswerte mit demselben Namen wie der geheime Name ab:</span><span class="sxs-lookup"><span data-stu-id="5657a-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="5657a-183">Nicht hierarchische Werte: Der `SecretName` Wert `config["SecretName"]`für wird mit abgerufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="5657a-184">Hierarchische Werte (Abschnitte): `:` Verwenden Sie die `GetSection` Notation (Kolon) oder die Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="5657a-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="5657a-185">Verwenden Sie einen der folgenden Ansätze, um den Konfigurationswert zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="5657a-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="5657a-186">Das X.509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="5657a-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="5657a-187">Die App <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ruft Mit Werten auf, die von der Datei *appsettings.json* bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5657a-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="5657a-188">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="5657a-188">Example values:</span></span>

* <span data-ttu-id="5657a-189">Name des Schlüsseltresors:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5657a-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="5657a-190">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="5657a-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="5657a-191">Zertifikat-Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="5657a-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="5657a-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5657a-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="5657a-193">Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5657a-194">In der Entwicklungsumgebung werden geheime `_dev` Werte mit dem Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="5657a-195">In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="5657a-196">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5657a-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="5657a-197">**Eine in Azure bereitgestellte App** kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, wodurch sich die App mit Azure Key Vault mithilfe der Azure AD-Authentifizierung ohne in der App gespeicherte Anmeldeinformationen (Anwendungs-ID und Kennwort/geheime sende Client) authentifizieren kann.</span><span class="sxs-lookup"><span data-stu-id="5657a-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="5657a-198">Die Beispiel-App verwendet verwaltete `#define` Identitäten für Azure-Ressourcen, wenn `Managed`die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="5657a-199">Geben Sie den Tresornamen in die *Appappappsettings.json-Datei* ein.</span><span class="sxs-lookup"><span data-stu-id="5657a-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="5657a-200">Die Beispiel-App benötigt keine Anwendungs-ID und kein Kennwort `Managed` (Client Secret), wenn sie auf die Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="5657a-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="5657a-201">Die App wird in Azure bereitgestellt, und Azure authentifiziert die App für den Zugriff auf Azure Key Vault nur unter Verwendung des Tresornamens, der in der Datei *appsettings.json* gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="5657a-202">Stellen Sie die Beispiel-App in Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="5657a-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="5657a-203">Eine in Azure App Service bereitgestellte App wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="5657a-204">Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung im folgenden Befehl ab.</span><span class="sxs-lookup"><span data-stu-id="5657a-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="5657a-205">Die Objekt-ID wird im Azure-Portal im **Identitätsbedienfeld** des App-Dienstes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="5657a-206">Stellen Sie der App mithilfe der Azure CLI `list` `get` und der Objekt-ID der App Berechtigungen für den Zugriff auf den Schlüsseltresor zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="5657a-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="5657a-207">**Starten Sie die App** mit Azure CLI, PowerShell oder dem Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="5657a-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="5657a-208">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="5657a-208">The sample app:</span></span>

* <span data-ttu-id="5657a-209">Erstellt eine Instanz `AzureServiceTokenProvider` der Klasse ohne Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="5657a-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="5657a-210">Wenn keine Verbindungszeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="5657a-211">Mit <xref:Microsoft.Azure.KeyVault.KeyVaultClient> dem `AzureServiceTokenProvider` Instance-Tokenrückruf wird ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="5657a-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="5657a-212">Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Standardimplementierung verwendet, die alle geheimen`--`Werte lädt und`:`Doppelstriche ( ) durch Doppelpunkte ( ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="5657a-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="5657a-213">Beispielwert für den Schlüsseltresornamen:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5657a-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="5657a-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5657a-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="5657a-215">Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5657a-216">In der Entwicklungsumgebung haben `_dev` geheime Werte das Suffix, da sie von Benutzergeheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="5657a-217">In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen, da sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="5657a-218">Wenn sie `Access denied` eine Fehlermeldung erhalten, vergewissern Sie sich, dass die App bei Azure AD registriert ist und Zugriff auf den Schlüsseltresor gewährt wurde.</span><span class="sxs-lookup"><span data-stu-id="5657a-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="5657a-219">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="5657a-220">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure DevOps-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager-Dienstverbindung zu einer VM mit einer verwalteten Dienstidentität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="5657a-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="5657a-221">Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="5657a-221">Configuration options</span></span>

<span data-ttu-id="5657a-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>kann eine <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="5657a-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="5657a-223">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="5657a-223">Property</span></span>         | <span data-ttu-id="5657a-224">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="5657a-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="5657a-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>zum Abrufen von Werten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5657a-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="5657a-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Instanz, die verwendet wird, um das geheime Laden zu steuern.</span><span class="sxs-lookup"><span data-stu-id="5657a-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="5657a-227">`Timespan`zwischen den Versuchen zu warten, den Schlüsseltresor nach Änderungen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="5657a-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="5657a-228">Der Standardwert `null` ist (Konfiguration wird nicht neu geladen).</span><span class="sxs-lookup"><span data-stu-id="5657a-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="5657a-229">Schlüsseltresor-URI.</span><span class="sxs-lookup"><span data-stu-id="5657a-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="5657a-230">Verwenden eines Schlüsselnamenspräfixes</span><span class="sxs-lookup"><span data-stu-id="5657a-230">Use a key name prefix</span></span>

<span data-ttu-id="5657a-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bietet eine Überladung, die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>eine Implementierung von akzeptiert, mit der Sie steuern können, wie Schlüsseltresorgeheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="5657a-232">Sie können die Schnittstelle beispielsweise implementieren, um geheime Werte basierend auf einem Präfixwert zu laden, den Sie beim App-Start angeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="5657a-233">Auf diese Weise können Sie z. B. Geheimnisse basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="5657a-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="5657a-234">Verwenden Sie keine Präfixe für wichtige Tresorgeheimnisse, um Geheimnisse für mehrere Apps in demselben Schlüsseltresor zu platzieren oder Umweltgeheimnisse (z. B. *Entwicklungs-* und *Produktionsgeheimnisse)* in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="5657a-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="5657a-235">Es wird empfohlen, dass verschiedene Apps und Entwicklungs-/Produktionsumgebungen separate Schlüsseltresore verwenden, um App-Umgebungen für ein Höchstmaß an Sicherheit zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="5657a-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="5657a-236">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüsseltresor (und mit dem `5000-AppSecret` Geheimen Manager-Tool für die Entwicklungsumgebung) für (Zeiträume sind in geheimen Schlüsseltresornamen nicht zulässig) eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5657a-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="5657a-237">Dieser Geheime stellt einen Geheimschlüssel für die Version 5.0.0.0 der App dar.</span><span class="sxs-lookup"><span data-stu-id="5657a-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="5657a-238">Für eine andere Version der App, 5.1.0.0, wird dem Schlüsseltresor (und `5100-AppSecret`der Verwendung des Geheimen Managers) für ein geheimer Schlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5657a-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="5657a-239">Jede App-Version lädt ihren versionierten `AppSecret`geheimen Wert in ihre Konfiguration als , um die Version zu entfernen, während sie den geheimen Wert lädt.</span><span class="sxs-lookup"><span data-stu-id="5657a-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="5657a-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>benutzerdefinierten aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="5657a-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="5657a-241">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versionspräfixe von Geheimnissen, um den richtigen Geheimschlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="5657a-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="5657a-242">`Load`lädt einen geheimen Schlüssel, wenn der Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="5657a-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="5657a-243">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="5657a-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="5657a-244">`GetKey`:</span></span>
  * <span data-ttu-id="5657a-245">Entfernt das Präfix aus dem geheimen Namen.</span><span class="sxs-lookup"><span data-stu-id="5657a-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="5657a-246">Ersetzt zwei Bindestriche in `KeyDelimiter`einem beliebigen Namen durch die , die das Trennzeichen ist, das in der Konfiguration verwendet wird (in der Regel ein Doppelpunkt).</span><span class="sxs-lookup"><span data-stu-id="5657a-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="5657a-247">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="5657a-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="5657a-248">Die `Load` Methode wird von einem Anbieteralgorithmus aufgerufen, der durch die Tresorgeheimnisse iteriert, um diejenigen zu finden, die das Versionspräfix haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="5657a-249">Wenn ein Versionspräfix `Load`mit gefunden wird, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des geheimen Namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="5657a-250">Das Versionspräfix wird vom Namen des Geheimen entfernt und der Rest des geheimen Namens zum Laden in die Konfigurationsnamen-Wert-Paare der App zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="5657a-251">Wenn dieser Ansatz implementiert wird:</span><span class="sxs-lookup"><span data-stu-id="5657a-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="5657a-252">Die in der Projektdatei der App angegebene App-Version.</span><span class="sxs-lookup"><span data-stu-id="5657a-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="5657a-253">Im folgenden Beispiel ist die App-Version `5.0.0.0`auf:</span><span class="sxs-lookup"><span data-stu-id="5657a-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5657a-254">Vergewissern `<UserSecretsId>` Sie sich, dass eine Eigenschaft `{GUID}` in der Projektdatei der App vorhanden ist, wobei sich eine vom Benutzer bereitgestellte GUID befindet:</span><span class="sxs-lookup"><span data-stu-id="5657a-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="5657a-255">Speichern Sie die folgenden Geheimnisse lokal mit dem [Secret Manager-Tool:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="5657a-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="5657a-256">Geheimnisse werden in Azure Key Vault mit den folgenden Azure CLI-Befehlen gespeichert:</span><span class="sxs-lookup"><span data-stu-id="5657a-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="5657a-257">Wenn die App ausgeführt wird, werden die Schlüsseltresorgeheimnisse geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="5657a-258">Der geheime `5000-AppSecret` Zeichenfolgenschlüssel für wird mit der in der Projektdatei der App angegebenen App-Version (`5.0.0.0`) abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="5657a-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="5657a-259">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="5657a-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="5657a-260">In der gesamten App wird `AppSecret` der geheime Wert in der gesamten App beim Lesen der Konfiguration mit dem Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="5657a-261">Wenn die Version der App in der `5.1.0.0` Projektdatei geändert wird und die `5.1.0.0_secret_value_dev` App erneut ausgeführt `5.1.0.0_secret_value_prod` wird, wird der zurückgegebene geheime Wert in der Entwicklungsumgebung und in der Produktion zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="5657a-262">Sie können auch <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Ihre <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>eigene Implementierung für bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5657a-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="5657a-263">Ein benutzerdefinierter Client ermöglicht die Freigabe einer einzelnen Instanz des Clients in der App.</span><span class="sxs-lookup"><span data-stu-id="5657a-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5657a-264">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5657a-264">Bind an array to a class</span></span>

<span data-ttu-id="5657a-265">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array zur Bindung an ein POCO-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="5657a-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="5657a-266">Beim Lesen aus einer Konfigurationsquelle, die`:`es Tasten ermöglicht, Doppelpunkte ( ) Trennzeichen zu enthalten, `:1:` &hellip; `:{n}:`wird ein numerisches Schlüsselsegment verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array besteht ( ,`:0:`, ).</span><span class="sxs-lookup"><span data-stu-id="5657a-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="5657a-267">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5657a-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="5657a-268">Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="5657a-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="5657a-269">Der in diesem Thema beschriebene Ansatz`--`verwendet doppelte Bindestriche ( ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="5657a-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="5657a-270">Arrayschlüssel`--0--`werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( , `--1--`, &hellip; `--{n}--`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5657a-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="5657a-271">Untersuchen Sie die folgende [Serilog-Protokollierungsanbieterkonfiguration,](https://serilog.net/) die von einer JSON-Datei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="5657a-272">Es sind zwei Objektliterale `WriteTo` im Array definiert, die zwei Serilog-Senken widerspiegeln, die Ziele für die Protokollierungsausgabe beschreiben: *sinks*</span><span class="sxs-lookup"><span data-stu-id="5657a-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="5657a-273">Die in der vorherigen JSON-Datei gezeigte Konfiguration wird`--`in Azure Key Vault mit doppelter Bindestrich - ) Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="5657a-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="5657a-274">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5657a-274">Key</span></span> | <span data-ttu-id="5657a-275">Wert</span><span class="sxs-lookup"><span data-stu-id="5657a-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="5657a-276">Geheimnisse neu laden</span><span class="sxs-lookup"><span data-stu-id="5657a-276">Reload secrets</span></span>

<span data-ttu-id="5657a-277">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="5657a-278">Abgelaufene, deaktivierte und aktualisierte Geheimnisse im Schlüsseltresor `Reload` werden von der App erst respektiert, wenn sie ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="5657a-279">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="5657a-279">Disabled and expired secrets</span></span>

<span data-ttu-id="5657a-280">Deaktivierte und abgelaufene Geheimnisse werfen eine <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="5657a-281">Um zu verhindern, dass die App ausgeworfen wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurationsanbieters bereit, oder aktualisieren Sie den deaktivierten oder abgelaufenen geheimen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="5657a-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5657a-282">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5657a-282">Troubleshoot</span></span>

<span data-ttu-id="5657a-283">Wenn die App die Konfiguration nicht über den Anbieter lädt, wird eine Fehlermeldung in die [ASP.NET Core Logging Infrastructure](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="5657a-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5657a-284">Die folgenden Bedingungen verhindern das Laden der Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="5657a-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="5657a-285">Die App oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5657a-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="5657a-286">Der Schlüsseltresor ist in Azure Key Vault nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5657a-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="5657a-287">Die App ist nicht berechtigt, auf den Schlüsseltresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5657a-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="5657a-288">Die Zugriffsrichtlinie enthält `Get` keine `List` Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="5657a-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="5657a-289">Im Schlüsseltresor werden die Konfigurationsdaten (Name-Wert-Paar) fälschlicherweise benannt, fehlen, deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="5657a-290">Die App hat den falschen`KeyVaultName`Schlüsseltresornamen (`AzureADApplicationId`), Azure AD Application`AzureADCertThumbprint`Id ( ), oder Azure AD-Zertifikatfingerabdruck ( ).</span><span class="sxs-lookup"><span data-stu-id="5657a-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="5657a-291">Der Konfigurationsschlüssel (Name) ist in der App für den Wert, den Sie laden möchten, falsch.</span><span class="sxs-lookup"><span data-stu-id="5657a-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="5657a-292">Beim Hinzufügen der Zugriffsrichtlinie für die App zum Schlüsseltresor wurde die Richtlinie erstellt, die Schaltfläche **Speichern** wurde jedoch nicht in der Benutzeroberfläche **für Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5657a-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5657a-293">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5657a-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="5657a-294">Microsoft Azure: Schlüsseltresor</span><span class="sxs-lookup"><span data-stu-id="5657a-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="5657a-295">Microsoft Azure: Wichtige Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="5657a-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="5657a-296">Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5657a-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="5657a-297">KeyVaultClient-Klasse</span><span class="sxs-lookup"><span data-stu-id="5657a-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="5657a-298">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="5657a-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="5657a-299">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="5657a-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5657a-300">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault-Konfigurationsanbieter](https://azure.microsoft.com/services/key-vault/) verwenden, um App-Konfigurationswerte aus Den Geheimnissen von Azure Key Vault zu laden.</span><span class="sxs-lookup"><span data-stu-id="5657a-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="5657a-301">Azure Key Vault ist ein cloudbasierter Dienst, der beim Schutz kryptografischer Schlüssel und Geheimnisse von Apps und Diensten unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="5657a-302">Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-Apps sind:</span><span class="sxs-lookup"><span data-stu-id="5657a-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="5657a-303">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="5657a-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="5657a-304">Erfüllung der Anforderung für FIPS 140-2 Level 2 validierte Hardware-Sicherheitsmodule (HSM) beim Speichern von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="5657a-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="5657a-305">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5657a-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="5657a-306">Pakete</span><span class="sxs-lookup"><span data-stu-id="5657a-306">Packages</span></span>

<span data-ttu-id="5657a-307">Fügen Sie dem [Microsoft.Extensions.Configuration.AzureKeyVault-Paket](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="5657a-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="5657a-308">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="5657a-308">Sample app</span></span>

<span data-ttu-id="5657a-309">Die Beispiel-App wird in einem der `#define` beiden Modi ausgeführt, die durch die Anweisung oben in der *Program.cs-Datei* bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="5657a-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="5657a-310">`Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X.509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="5657a-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="5657a-311">Diese Version des Beispiels kann von jedem Speicherort aus ausgeführt, in Azure App Service oder auf jedem Host bereitgestellt werden, der eine ASP.NET Core-App bereitgestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5657a-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="5657a-312">`Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die App bei Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen zu authentifizieren, die im Code oder in der Konfiguration der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="5657a-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="5657a-313">Wenn verwaltete Identitäten zur Authentifizierung verwendet werden, sind keine Azure AD-Anwendungs-ID und ein Kennwort (Client Secret) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5657a-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="5657a-314">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="5657a-315">Befolgen Sie die Anleitung im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="5657a-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="5657a-316">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe`#define`von <xref:index#preprocessor-directives-in-sample-code>Präprozessordirektiven ( ) finden Sie unter .</span><span class="sxs-lookup"><span data-stu-id="5657a-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="5657a-317">Geheime Speicherung in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="5657a-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="5657a-318">Legen Sie Geheimnisse lokal mit dem [Secret Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="5657a-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5657a-319">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Secret Manager-Speicher geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="5657a-320">Das Tool "Geheimer Manager" erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="5657a-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="5657a-321">Legen Sie den`{GUID}`Eigenschaftswert ( ) auf eine beliebige eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="5657a-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="5657a-322">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="5657a-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="5657a-323">Hierarchische Werte (Konfigurationsabschnitte) `:` verwenden ein (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfigurationsschlüsselnamen.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="5657a-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="5657a-324">Der geheime Manager wird von einer Befehlsshell verwendet, `{SECRET NAME}` die zum `{SECRET VALUE}` [Inhaltsstamm](xref:fundamentals/index#content-root)des Projekts geöffnet wird, wobei der Name und der Wert der Folgende ist:</span><span class="sxs-lookup"><span data-stu-id="5657a-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="5657a-325">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhaltsstamm](xref:fundamentals/index#content-root) des Projekts aus, um die Geheimnisse für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5657a-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="5657a-326">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in `_prod`geändert.</span><span class="sxs-lookup"><span data-stu-id="5657a-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="5657a-327">Das Suffix bietet einen visuellen Hinweis in der Ausgabe der App, der die Quelle der Konfigurationswerte angibt.</span><span class="sxs-lookup"><span data-stu-id="5657a-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="5657a-328">Geheimspeicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5657a-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="5657a-329">Die Anweisungen des [Schnellstarts: Festlegen und Abrufen eines geheimen Schlüssels aus Azure Key Vault mithilfe des Azure](/azure/key-vault/quick-create-cli) CLI-Themas sind hier zusammengefasst, um einen Azure Key Vault zu erstellen und geheime Informationen zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="5657a-330">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="5657a-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="5657a-331">Öffnen Sie die Azure Cloud-Shell mit einer der folgenden Methoden im [Azure-Portal:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="5657a-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="5657a-332">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="5657a-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="5657a-333">Verwenden Sie die Suchzeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="5657a-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="5657a-334">Öffnen Sie Cloud Shell in Ihrem Browser mit der **Schaltfläche Cloud Shell starten.**</span><span class="sxs-lookup"><span data-stu-id="5657a-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="5657a-335">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="5657a-336">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und Übersicht über Azure [Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="5657a-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="5657a-337">Wenn Sie noch nicht authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="5657a-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="5657a-338">Erstellen Sie eine Ressourcengruppe mit `{RESOURCE GROUP NAME}` dem folgenden Befehl, wobei der `{LOCATION}` Name der Ressourcengruppe für die neue Ressourcengruppe und die Azure-Region (Datencenter) lautet:</span><span class="sxs-lookup"><span data-stu-id="5657a-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5657a-339">Erstellen Sie einen Schlüsseltresor in der Ressourcengruppe mit dem folgenden Befehl, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüsseltresor und `{LOCATION}` die Azure-Region (Datencenter) lautet:</span><span class="sxs-lookup"><span data-stu-id="5657a-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="5657a-340">Erstellen Sie Geheimnisse im Schlüsseltresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5657a-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="5657a-341">Geheime Namen von Azure Key Vault sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5657a-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="5657a-342">Hierarchische Werte (Konfigurationsabschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5657a-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5657a-343">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index)zu trennen, sind in geheimen Schlüsseltresornamen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="5657a-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="5657a-344">Daher werden zwei Bindestriche verwendet und gegen einen Doppelpunkt getauscht, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="5657a-345">Die folgenden Geheimnisse sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="5657a-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="5657a-346">Die Werte `_prod` enthalten ein Suffix, `_dev` um sie von den in der Entwicklungsumgebung geladenen Suffixwerten von Benutzergeheimnissen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="5657a-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="5657a-347">Ersetzen `{KEY VAULT NAME}` Sie dies durch den Namen des Schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="5657a-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="5657a-348">Verwenden von Anwendungs-ID und X.509-Zertifikat für nicht von Azure gehostete Apps</span><span class="sxs-lookup"><span data-stu-id="5657a-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="5657a-349">Konfigurieren Sie Azure AD, Azure Key Vault und die App so, dass sie eine Azure Active Directory-Anwendungs-ID und ein X.509-Zertifikat verwenden, um sich bei einem Schlüsseltresor zu authentifizieren, **wenn die App außerhalb von Azure gehostet wird.**</span><span class="sxs-lookup"><span data-stu-id="5657a-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="5657a-350">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="5657a-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="5657a-351">Obwohl die Verwendung einer Anwendungs-ID und eines X.509-Zertifikats für in Azure gehostete Apps unterstützt wird, wird empfohlen, [verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer App in Azure zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5657a-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="5657a-352">Verwaltete Identitäten erfordern nicht das Speichern eines Zertifikats in der App oder in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5657a-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="5657a-353">Die Beispiel-App verwendet eine Anwendungs-ID und `#define` ein X.509-Zertifikat, `Certificate`wenn die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="5657a-354">Erstellen Sie ein PKCS-12-Archiv (*.pfx*) Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="5657a-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="5657a-355">Zu den Optionen zum Erstellen von Zertifikaten gehören [MakeCert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="5657a-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="5657a-356">Installieren Sie das Zertifikat im persönlichen Zertifikatspeicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="5657a-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="5657a-357">Die Markierung des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="5657a-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="5657a-358">Beachten Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="5657a-359">Exportieren Sie das PKCS-12-Archiv (*.pfx*) zertifikat als DER-codiertes Zertifikat (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="5657a-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="5657a-360">Registrieren Sie die App bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="5657a-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="5657a-361">Laden Sie das DER-codierte Zertifikat (*.cer*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="5657a-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="5657a-362">Wählen Sie die App in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="5657a-363">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="5657a-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="5657a-364">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="5657a-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="5657a-365">Ein *.cer*-, *.pem*- oder *.crt-Zertifikat* ist zulässig.</span><span class="sxs-lookup"><span data-stu-id="5657a-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="5657a-366">Speichern Sie den Schlüsseltresornamen, die Anwendungs-ID und den Zertifikatfingerabdruck in der *App-Datei appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="5657a-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="5657a-367">Navigieren Sie zu **Schlüsseltresoren** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="5657a-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="5657a-368">Wählen Sie den Schlüsseltresor aus, den Sie im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="5657a-369">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="5657a-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="5657a-370">Wählen Sie **Zugriffsrichtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="5657a-371">Öffnen Sie **geheime Berechtigungen,** und stellen Sie der App **Berechtigung zum Abrufen** und **Auflisten** zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5657a-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="5657a-372">Wählen Sie **Prinzipal auswählen** aus, und wählen Sie die registrierte App nach Namen aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="5657a-373">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="5657a-374">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5657a-374">Select **OK**.</span></span>
1. <span data-ttu-id="5657a-375">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-375">Select **Save**.</span></span>
1. <span data-ttu-id="5657a-376">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="5657a-376">Deploy the app.</span></span>

<span data-ttu-id="5657a-377">Die `Certificate` Beispiel-App ruft ihre `IConfigurationRoot` Konfigurationswerte mit demselben Namen wie der geheime Name ab:</span><span class="sxs-lookup"><span data-stu-id="5657a-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="5657a-378">Nicht hierarchische Werte: Der `SecretName` Wert `config["SecretName"]`für wird mit abgerufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="5657a-379">Hierarchische Werte (Abschnitte): `:` Verwenden Sie die `GetSection` Notation (Kolon) oder die Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="5657a-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="5657a-380">Verwenden Sie einen der folgenden Ansätze, um den Konfigurationswert zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="5657a-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="5657a-381">Das X.509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="5657a-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="5657a-382">Die App <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> ruft Mit Werten auf, die von der Datei *appsettings.json* bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5657a-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="5657a-383">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="5657a-383">Example values:</span></span>

* <span data-ttu-id="5657a-384">Name des Schlüsseltresors:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5657a-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="5657a-385">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="5657a-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="5657a-386">Zertifikat-Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="5657a-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="5657a-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5657a-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="5657a-388">Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5657a-389">In der Entwicklungsumgebung werden geheime `_dev` Werte mit dem Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="5657a-390">In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="5657a-391">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5657a-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="5657a-392">**Eine in Azure bereitgestellte App** kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, wodurch sich die App mit Azure Key Vault mithilfe der Azure AD-Authentifizierung ohne in der App gespeicherte Anmeldeinformationen (Anwendungs-ID und Kennwort/geheime sende Client) authentifizieren kann.</span><span class="sxs-lookup"><span data-stu-id="5657a-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="5657a-393">Die Beispiel-App verwendet verwaltete `#define` Identitäten für Azure-Ressourcen, wenn `Managed`die Anweisung oben in der *Program.cs-Datei* auf festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="5657a-394">Geben Sie den Tresornamen in die *Appappappsettings.json-Datei* ein.</span><span class="sxs-lookup"><span data-stu-id="5657a-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="5657a-395">Die Beispiel-App benötigt keine Anwendungs-ID und kein Kennwort `Managed` (Client Secret), wenn sie auf die Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="5657a-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="5657a-396">Die App wird in Azure bereitgestellt, und Azure authentifiziert die App für den Zugriff auf Azure Key Vault nur unter Verwendung des Tresornamens, der in der Datei *appsettings.json* gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="5657a-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="5657a-397">Stellen Sie die Beispiel-App in Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="5657a-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="5657a-398">Eine in Azure App Service bereitgestellte App wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="5657a-399">Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung im folgenden Befehl ab.</span><span class="sxs-lookup"><span data-stu-id="5657a-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="5657a-400">Die Objekt-ID wird im Azure-Portal im **Identitätsbedienfeld** des App-Dienstes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="5657a-401">Stellen Sie der App mithilfe der Azure CLI `list` `get` und der Objekt-ID der App Berechtigungen für den Zugriff auf den Schlüsseltresor zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="5657a-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="5657a-402">**Starten Sie die App** mit Azure CLI, PowerShell oder dem Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="5657a-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="5657a-403">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="5657a-403">The sample app:</span></span>

* <span data-ttu-id="5657a-404">Erstellt eine Instanz `AzureServiceTokenProvider` der Klasse ohne Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="5657a-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="5657a-405">Wenn keine Verbindungszeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="5657a-406">Mit <xref:Microsoft.Azure.KeyVault.KeyVaultClient> dem `AzureServiceTokenProvider` Instance-Tokenrückruf wird ein neues erstellt.</span><span class="sxs-lookup"><span data-stu-id="5657a-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="5657a-407">Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Standardimplementierung verwendet, die alle geheimen`--`Werte lädt und`:`Doppelstriche ( ) durch Doppelpunkte ( ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="5657a-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="5657a-408">Beispielwert für den Schlüsseltresornamen:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="5657a-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="5657a-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5657a-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="5657a-410">Wenn Sie die App ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5657a-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="5657a-411">In der Entwicklungsumgebung haben `_dev` geheime Werte das Suffix, da sie von Benutzergeheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="5657a-412">In der Produktionsumgebung werden die `_prod` Werte mit dem Suffix geladen, da sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="5657a-413">Wenn sie `Access denied` eine Fehlermeldung erhalten, vergewissern Sie sich, dass die App bei Azure AD registriert ist und Zugriff auf den Schlüsseltresor gewährt wurde.</span><span class="sxs-lookup"><span data-stu-id="5657a-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="5657a-414">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="5657a-415">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure DevOps-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager-Dienstverbindung zu einer VM mit einer verwalteten Dienstidentität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="5657a-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="5657a-416">Verwenden eines Schlüsselnamenspräfixes</span><span class="sxs-lookup"><span data-stu-id="5657a-416">Use a key name prefix</span></span>

<span data-ttu-id="5657a-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bietet eine Überladung, die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>eine Implementierung von akzeptiert, mit der Sie steuern können, wie Schlüsseltresorgeheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="5657a-418">Sie können die Schnittstelle beispielsweise implementieren, um geheime Werte basierend auf einem Präfixwert zu laden, den Sie beim App-Start angeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="5657a-419">Auf diese Weise können Sie z. B. Geheimnisse basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="5657a-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="5657a-420">Verwenden Sie keine Präfixe für wichtige Tresorgeheimnisse, um Geheimnisse für mehrere Apps in demselben Schlüsseltresor zu platzieren oder Umweltgeheimnisse (z. B. *Entwicklungs-* und *Produktionsgeheimnisse)* in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="5657a-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="5657a-421">Es wird empfohlen, dass verschiedene Apps und Entwicklungs-/Produktionsumgebungen separate Schlüsseltresore verwenden, um App-Umgebungen für ein Höchstmaß an Sicherheit zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="5657a-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="5657a-422">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüsseltresor (und mit dem `5000-AppSecret` Geheimen Manager-Tool für die Entwicklungsumgebung) für (Zeiträume sind in geheimen Schlüsseltresornamen nicht zulässig) eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5657a-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="5657a-423">Dieser Geheime stellt einen Geheimschlüssel für die Version 5.0.0.0 der App dar.</span><span class="sxs-lookup"><span data-stu-id="5657a-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="5657a-424">Für eine andere Version der App, 5.1.0.0, wird dem Schlüsseltresor (und `5100-AppSecret`der Verwendung des Geheimen Managers) für ein geheimer Schlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5657a-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="5657a-425">Jede App-Version lädt ihren versionierten `AppSecret`geheimen Wert in ihre Konfiguration als , um die Version zu entfernen, während sie den geheimen Wert lädt.</span><span class="sxs-lookup"><span data-stu-id="5657a-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="5657a-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>benutzerdefinierten aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="5657a-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="5657a-427">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versionspräfixe von Geheimnissen, um den richtigen Geheimschlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="5657a-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="5657a-428">`Load`lädt einen geheimen Schlüssel, wenn der Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="5657a-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="5657a-429">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="5657a-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="5657a-430">`GetKey`:</span></span>
  * <span data-ttu-id="5657a-431">Entfernt das Präfix aus dem geheimen Namen.</span><span class="sxs-lookup"><span data-stu-id="5657a-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="5657a-432">Ersetzt zwei Bindestriche in `KeyDelimiter`einem beliebigen Namen durch die , die das Trennzeichen ist, das in der Konfiguration verwendet wird (in der Regel ein Doppelpunkt).</span><span class="sxs-lookup"><span data-stu-id="5657a-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="5657a-433">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="5657a-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="5657a-434">Die `Load` Methode wird von einem Anbieteralgorithmus aufgerufen, der durch die Tresorgeheimnisse iteriert, um diejenigen zu finden, die das Versionspräfix haben.</span><span class="sxs-lookup"><span data-stu-id="5657a-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="5657a-435">Wenn ein Versionspräfix `Load`mit gefunden wird, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des geheimen Namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="5657a-436">Das Versionspräfix wird vom Namen des Geheimen entfernt und der Rest des geheimen Namens zum Laden in die Konfigurationsnamen-Wert-Paare der App zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="5657a-437">Wenn dieser Ansatz implementiert wird:</span><span class="sxs-lookup"><span data-stu-id="5657a-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="5657a-438">Die in der Projektdatei der App angegebene App-Version.</span><span class="sxs-lookup"><span data-stu-id="5657a-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="5657a-439">Im folgenden Beispiel ist die App-Version `5.0.0.0`auf:</span><span class="sxs-lookup"><span data-stu-id="5657a-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5657a-440">Vergewissern `<UserSecretsId>` Sie sich, dass eine Eigenschaft `{GUID}` in der Projektdatei der App vorhanden ist, wobei sich eine vom Benutzer bereitgestellte GUID befindet:</span><span class="sxs-lookup"><span data-stu-id="5657a-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="5657a-441">Speichern Sie die folgenden Geheimnisse lokal mit dem [Secret Manager-Tool:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="5657a-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="5657a-442">Geheimnisse werden in Azure Key Vault mit den folgenden Azure CLI-Befehlen gespeichert:</span><span class="sxs-lookup"><span data-stu-id="5657a-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="5657a-443">Wenn die App ausgeführt wird, werden die Schlüsseltresorgeheimnisse geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="5657a-444">Der geheime `5000-AppSecret` Zeichenfolgenschlüssel für wird mit der in der Projektdatei der App angegebenen App-Version (`5.0.0.0`) abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="5657a-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="5657a-445">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="5657a-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="5657a-446">In der gesamten App wird `AppSecret` der geheime Wert in der gesamten App beim Lesen der Konfiguration mit dem Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="5657a-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="5657a-447">Wenn die Version der App in der `5.1.0.0` Projektdatei geändert wird und die `5.1.0.0_secret_value_dev` App erneut ausgeführt `5.1.0.0_secret_value_prod` wird, wird der zurückgegebene geheime Wert in der Entwicklungsumgebung und in der Produktion zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5657a-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="5657a-448">Sie können auch <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Ihre <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>eigene Implementierung für bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5657a-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="5657a-449">Ein benutzerdefinierter Client ermöglicht die Freigabe einer einzelnen Instanz des Clients in der App.</span><span class="sxs-lookup"><span data-stu-id="5657a-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5657a-450">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5657a-450">Bind an array to a class</span></span>

<span data-ttu-id="5657a-451">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array zur Bindung an ein POCO-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="5657a-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="5657a-452">Beim Lesen aus einer Konfigurationsquelle, die`:`es Tasten ermöglicht, Doppelpunkte ( ) Trennzeichen zu enthalten, `:1:` &hellip; `:{n}:`wird ein numerisches Schlüsselsegment verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array besteht ( ,`:0:`, ).</span><span class="sxs-lookup"><span data-stu-id="5657a-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="5657a-453">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5657a-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="5657a-454">Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="5657a-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="5657a-455">Der in diesem Thema beschriebene Ansatz`--`verwendet doppelte Bindestriche ( ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="5657a-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="5657a-456">Arrayschlüssel`--0--`werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( , `--1--`, &hellip; `--{n}--`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5657a-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="5657a-457">Untersuchen Sie die folgende [Serilog-Protokollierungsanbieterkonfiguration,](https://serilog.net/) die von einer JSON-Datei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="5657a-458">Es sind zwei Objektliterale `WriteTo` im Array definiert, die zwei Serilog-Senken widerspiegeln, die Ziele für die Protokollierungsausgabe beschreiben: *sinks*</span><span class="sxs-lookup"><span data-stu-id="5657a-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="5657a-459">Die in der vorherigen JSON-Datei gezeigte Konfiguration wird`--`in Azure Key Vault mit doppelter Bindestrich - ) Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="5657a-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="5657a-460">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5657a-460">Key</span></span> | <span data-ttu-id="5657a-461">Wert</span><span class="sxs-lookup"><span data-stu-id="5657a-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="5657a-462">Geheimnisse neu laden</span><span class="sxs-lookup"><span data-stu-id="5657a-462">Reload secrets</span></span>

<span data-ttu-id="5657a-463">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5657a-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="5657a-464">Abgelaufene, deaktivierte und aktualisierte Geheimnisse im Schlüsseltresor `Reload` werden von der App erst respektiert, wenn sie ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5657a-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="5657a-465">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="5657a-465">Disabled and expired secrets</span></span>

<span data-ttu-id="5657a-466">Deaktivierte und abgelaufene Geheimnisse werfen eine <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>aus.</span><span class="sxs-lookup"><span data-stu-id="5657a-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="5657a-467">Um zu verhindern, dass die App ausgeworfen wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurationsanbieters bereit, oder aktualisieren Sie den deaktivierten oder abgelaufenen geheimen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="5657a-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5657a-468">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5657a-468">Troubleshoot</span></span>

<span data-ttu-id="5657a-469">Wenn die App die Konfiguration nicht über den Anbieter lädt, wird eine Fehlermeldung in die [ASP.NET Core Logging Infrastructure](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="5657a-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5657a-470">Die folgenden Bedingungen verhindern das Laden der Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="5657a-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="5657a-471">Die App oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5657a-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="5657a-472">Der Schlüsseltresor ist in Azure Key Vault nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5657a-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="5657a-473">Die App ist nicht berechtigt, auf den Schlüsseltresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5657a-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="5657a-474">Die Zugriffsrichtlinie enthält `Get` keine `List` Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="5657a-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="5657a-475">Im Schlüsseltresor werden die Konfigurationsdaten (Name-Wert-Paar) fälschlicherweise benannt, fehlen, deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="5657a-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="5657a-476">Die App hat den falschen`KeyVaultName`Schlüsseltresornamen (`AzureADApplicationId`), Azure AD Application`AzureADCertThumbprint`Id ( ), oder Azure AD-Zertifikatfingerabdruck ( ).</span><span class="sxs-lookup"><span data-stu-id="5657a-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="5657a-477">Der Konfigurationsschlüssel (Name) ist in der App für den Wert, den Sie laden möchten, falsch.</span><span class="sxs-lookup"><span data-stu-id="5657a-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="5657a-478">Beim Hinzufügen der Zugriffsrichtlinie für die App zum Schlüsseltresor wurde die Richtlinie erstellt, die Schaltfläche **Speichern** wurde jedoch nicht in der Benutzeroberfläche **für Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5657a-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5657a-479">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5657a-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="5657a-480">Microsoft Azure: Schlüsseltresor</span><span class="sxs-lookup"><span data-stu-id="5657a-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="5657a-481">Microsoft Azure: Wichtige Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="5657a-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="5657a-482">Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5657a-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="5657a-483">KeyVaultClient-Klasse</span><span class="sxs-lookup"><span data-stu-id="5657a-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="5657a-484">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="5657a-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="5657a-485">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="5657a-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

