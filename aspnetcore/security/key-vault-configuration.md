---
title: Azure Key Vault Konfigurations Anbieters in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit dem Azure Key Vault-Konfigurations Anbieter eine App mithilfe von Name-Wert-Paaren konfigurieren, die zur Laufzeit geladen werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/key-vault-configuration
ms.openlocfilehash: 62c8b58dfa0272b1edc48f7e8b41475970ffd492
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777383"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="b5324-103">Azure Key Vault Konfigurations Anbieters in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b5324-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="b5324-104">Von [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b5324-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5324-105">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="b5324-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b5324-106">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b5324-107">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="b5324-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b5324-108">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="b5324-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b5324-109">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="b5324-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b5324-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5324-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b5324-111">Pakete</span><span class="sxs-lookup"><span data-stu-id="b5324-111">Packages</span></span>

<span data-ttu-id="b5324-112">Fügen Sie dem Paket " [Microsoft. Extensions. Configuration. azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5324-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b5324-113">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="b5324-113">Sample app</span></span>

<span data-ttu-id="b5324-114">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, `#define` die durch die-Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="b5324-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b5324-115">`Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="b5324-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b5324-116">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="b5324-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b5324-117">`Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b5324-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b5324-118">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b5324-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b5324-119">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b5324-120">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="b5324-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b5324-121">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven`#define`() finden <xref:index#preprocessor-directives-in-sample-code>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="b5324-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b5324-122">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="b5324-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="b5324-123">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="b5324-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b5324-124">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Speicher des geheimen Haupt Schlüssels geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b5324-125">Das Secret Manager-Tool erfordert `<UserSecretsId>` eine Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b5324-126">Legen Sie den-Eigenschafts Wert (`{GUID}`) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="b5324-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b5324-127">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5324-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b5324-128">Hierarchische Werte (Konfigurations Abschnitte) verwenden `:` einen (Doppelpunkt) als Trennzeichen in ASP.net Core Namen von [Konfigurations](xref:fundamentals/configuration/index) Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="b5324-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b5324-129">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts `{SECRET NAME}` geöffnet ist, wobei `{SECRET VALUE}` der Name und der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b5324-130">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="b5324-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b5324-131">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod`.</span><span class="sxs-lookup"><span data-stu-id="b5324-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b5324-132">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b5324-133">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b5324-134">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b5324-135">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="b5324-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b5324-136">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="b5324-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b5324-137">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="b5324-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b5324-138">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="b5324-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b5324-139">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="b5324-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b5324-140">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b5324-141">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b5324-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b5324-142">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich `az login` mit dem Befehl an.</span><span class="sxs-lookup"><span data-stu-id="b5324-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b5324-143">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, `{RESOURCE GROUP NAME}` wobei der Name der Ressourcengruppe für die neue Ressourcengruppe `{LOCATION}` und die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5324-144">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, `{KEY VAULT NAME}` wobei der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5324-145">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="b5324-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b5324-146">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b5324-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b5324-147">Hierarchische Werte (Konfigurations Abschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="b5324-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b5324-148">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="b5324-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b5324-149">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b5324-150">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b5324-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b5324-151">Die Werte enthalten ein `_prod` Suffix, um Sie von den `_dev` suffixwerten zu unterscheiden, die in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b5324-152">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="b5324-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b5324-153">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="b5324-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b5324-154">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="b5324-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="b5324-155">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b5324-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b5324-156">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b5324-157">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b5324-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b5324-158">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509 `#define` -Zertifikat, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Certificate`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b5324-159">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="b5324-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="b5324-160">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b5324-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b5324-161">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="b5324-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b5324-162">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="b5324-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b5324-163">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b5324-164">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="b5324-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="b5324-165">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="b5324-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="b5324-166">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="b5324-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="b5324-167">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b5324-168">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="b5324-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="b5324-169">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="b5324-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b5324-170">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="b5324-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b5324-171">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der *appSettings. JSON* -Datei der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="b5324-172">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="b5324-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b5324-173">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="b5324-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b5324-174">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="b5324-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="b5324-175">Wählen Sie **Zugriffs Richtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="b5324-176">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b5324-177">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b5324-178">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="b5324-179">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b5324-179">Select **OK**.</span></span>
1. <span data-ttu-id="b5324-180">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-180">Select **Save**.</span></span>
1. <span data-ttu-id="b5324-181">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-181">Deploy the app.</span></span>

<span data-ttu-id="b5324-182">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte `IConfigurationRoot` aus dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="b5324-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b5324-183">Nicht hierarchische Werte: der Wert für `SecretName` wird mit `config["SecretName"]`abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b5324-184">Hierarchische Werte (Abschnitte): `:` verwenden Sie die Notation (Doppel `GetSection` Punkt) oder die Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b5324-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b5324-185">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="b5324-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b5324-186">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="b5324-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b5324-187">Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der *appSettings. JSON* -Datei bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="b5324-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b5324-188">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="b5324-188">Example values:</span></span>

* <span data-ttu-id="b5324-189">Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5324-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b5324-190">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b5324-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b5324-191">Zertifikat Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b5324-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b5324-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5324-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="b5324-193">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5324-194">In der Entwicklungsumgebung werden geheime Werte mit dem `_dev` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b5324-195">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b5324-196">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5324-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b5324-197">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b5324-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b5324-198">In der Beispiel-App werden verwaltete Identitäten für Azure- `#define` Ressourcen verwendet, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Managed`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b5324-199">Geben Sie den Tresor Namen in die Datei " *appSettings. JSON* " der APP ein.</span><span class="sxs-lookup"><span data-stu-id="b5324-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="b5324-200">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) `Managed` , wenn Sie auf die-Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="b5324-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b5324-201">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei " *appSettings. JSON* " gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="b5324-202">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b5324-203">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b5324-204">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b5324-205">Die Objekt-ID wird im Azure-Portal im **Identitäts** Bereich der APP Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="b5324-206">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen `list` Sie `get` der APP die Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="b5324-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b5324-207">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="b5324-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b5324-208">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="b5324-208">The sample app:</span></span>

* <span data-ttu-id="b5324-209">Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="b5324-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b5324-210">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b5324-211">Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5324-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b5324-212">Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> -Instanz wird mit einer Standard Implementierung von <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> verwendet, die alle geheimen Werte lädt und doppelte Bindestriche (`--`) durch Doppelpunkte (`:`) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="b5324-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b5324-213">Beispiel Wert für Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5324-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b5324-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5324-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b5324-215">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5324-216">In der Entwicklungsumgebung verfügen geheime Werte über das `_dev` -Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b5324-217">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b5324-218">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="b5324-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b5324-219">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="b5324-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b5324-220">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b5324-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="b5324-221">Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="b5324-221">Configuration options</span></span>

<span data-ttu-id="b5324-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>kann akzeptieren <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span><span class="sxs-lookup"><span data-stu-id="b5324-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="b5324-223">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="b5324-223">Property</span></span>         | <span data-ttu-id="b5324-224">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="b5324-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="b5324-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>, der zum Abrufen von Werten verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="b5324-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="b5324-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>zum Steuern des geheimen Schlüssels verwendete Instanz.</span><span class="sxs-lookup"><span data-stu-id="b5324-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="b5324-227">`Timespan`, um zwischen versuchen zu warten, den Schlüssel Tresor auf Änderungen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="b5324-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="b5324-228">Der Standardwert ist `null` (die Konfiguration wird nicht neu geladen).</span><span class="sxs-lookup"><span data-stu-id="b5324-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="b5324-229">Key Vault-URI.</span><span class="sxs-lookup"><span data-stu-id="b5324-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b5324-230">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="b5324-230">Use a key name prefix</span></span>

<span data-ttu-id="b5324-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>stellt eine Überladung bereit, die eine <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementierung von akzeptiert, mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b5324-232">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="b5324-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b5324-233">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="b5324-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b5324-234">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="b5324-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b5324-235">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="b5324-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b5324-236">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="b5324-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b5324-237">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="b5324-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b5324-238">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) `5100-AppSecret`für hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b5324-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b5324-239">Jede APP-Version lädt den Wert für `AppSecret`die Versionierung mit Versions Angabe in die Konfiguration, wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b5324-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem benutzerdefinierten <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="b5324-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b5324-241">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="b5324-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b5324-242">`Load`lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="b5324-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b5324-243">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b5324-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b5324-244">`GetKey`:</span></span>
  * <span data-ttu-id="b5324-245">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="b5324-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b5324-246">Ersetzt zwei Bindestriche in einem beliebigen Namen durch `KeyDelimiter`das-Zeichen, das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b5324-247">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="b5324-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b5324-248">Die `Load` -Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="b5324-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b5324-249">Wenn ein Versions Präfix mit `Load`gefunden wird, verwendet der Algorithmus `GetKey` die-Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="b5324-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b5324-250">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="b5324-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b5324-251">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="b5324-252">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b5324-253">Im folgenden Beispiel wird die App-Version auf `5.0.0.0`festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b5324-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5324-254">Vergewissern Sie sich `<UserSecretsId>` , dass eine Eigenschaft in der Projektdatei der app vorhanden `{GUID}` ist, wobei eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b5324-255">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b5324-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b5324-256">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="b5324-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b5324-257">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b5324-258">Der Zeichen folgen Schlüssel `5000-AppSecret` für wird mit der in der Projektdatei der APP (`5.0.0.0`) angegebenen Version der APP abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="b5324-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b5324-259">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="b5324-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b5324-260">Während der gesamten APP wird beim Lesen der Konfiguration `AppSecret` mit dem Schlüssel der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b5324-261">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird `5.1.0.0_secret_value_dev` der geheime Wert in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktionsumgebung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b5324-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b5324-262">Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="b5324-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b5324-263">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="b5324-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b5324-264">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="b5324-264">Bind an array to a class</span></span>

<span data-ttu-id="b5324-265">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="b5324-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b5324-266">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen (`:`Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden`:0:`, `:1:`die &hellip; `:{n}:`ein Array bilden (,,).</span><span class="sxs-lookup"><span data-stu-id="b5324-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b5324-267">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b5324-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b5324-268">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b5324-269">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche (`--`) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="b5324-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b5324-270">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten`--0--`(, `--1--`, &hellip; `--{n}--`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b5324-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b5324-271">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="b5324-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b5324-272">Im `WriteTo` Array sind zwei Objektliterale definiert, die zwei serilog- *senken*reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="b5324-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b5324-273">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von`--`Double Dash ()-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="b5324-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b5324-274">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="b5324-274">Key</span></span> | <span data-ttu-id="b5324-275">Wert</span><span class="sxs-lookup"><span data-stu-id="b5324-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b5324-276">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="b5324-276">Reload secrets</span></span>

<span data-ttu-id="b5324-277">Geheimnisse werden zwischen `IConfigurationRoot.Reload()` gespeichert, bis aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b5324-278">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst `Reload` berücksichtigt, wenn der ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b5324-279">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="b5324-279">Disabled and expired secrets</span></span>

<span data-ttu-id="b5324-280">Deaktivierte und abgelaufene geheim <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>nisse lösen einen aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b5324-281">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="b5324-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5324-282">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b5324-282">Troubleshoot</span></span>

<span data-ttu-id="b5324-283">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b5324-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b5324-284">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="b5324-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b5324-285">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b5324-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b5324-286">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="b5324-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b5324-287">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b5324-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b5324-288">Die Zugriffs Richtlinie enthält `Get` nicht die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="b5324-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b5324-289">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b5324-290">Die APP hat den falschen Schlüssel Tresor Namen (`KeyVaultName`), Azure AD Anwendungs-ID`AzureADApplicationId`() oder Azure AD Zertifikat Fingerabdruck (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="b5324-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b5324-291">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="b5324-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b5324-292">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b5324-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5324-293">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5324-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b5324-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b5324-295">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="b5324-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b5324-296">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b5324-297">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="b5324-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b5324-298">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="b5324-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b5324-299">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="b5324-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5324-300">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="b5324-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="b5324-301">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="b5324-302">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="b5324-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="b5324-303">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="b5324-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="b5324-304">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="b5324-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="b5324-305">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b5324-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="b5324-306">Pakete</span><span class="sxs-lookup"><span data-stu-id="b5324-306">Packages</span></span>

<span data-ttu-id="b5324-307">Fügen Sie dem Paket " [Microsoft. Extensions. Configuration. azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="b5324-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="b5324-308">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="b5324-308">Sample app</span></span>

<span data-ttu-id="b5324-309">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, `#define` die durch die-Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="b5324-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="b5324-310">`Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="b5324-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="b5324-311">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="b5324-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="b5324-312">`Managed`&ndash; Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b5324-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="b5324-313">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b5324-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="b5324-314">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="b5324-315">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="b5324-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="b5324-316">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven`#define`() finden <xref:index#preprocessor-directives-in-sample-code>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="b5324-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="b5324-317">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="b5324-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="b5324-318">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="b5324-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="b5324-319">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Speicher des geheimen Haupt Schlüssels geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="b5324-320">Das Secret Manager-Tool erfordert `<UserSecretsId>` eine Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="b5324-321">Legen Sie den-Eigenschafts Wert (`{GUID}`) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="b5324-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="b5324-322">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5324-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="b5324-323">Hierarchische Werte (Konfigurations Abschnitte) verwenden `:` einen (Doppelpunkt) als Trennzeichen in ASP.net Core Namen von [Konfigurations](xref:fundamentals/configuration/index) Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="b5324-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="b5324-324">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts `{SECRET NAME}` geöffnet ist, wobei `{SECRET VALUE}` der Name und der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="b5324-325">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="b5324-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="b5324-326">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod`.</span><span class="sxs-lookup"><span data-stu-id="b5324-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="b5324-327">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="b5324-328">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="b5324-329">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="b5324-330">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="b5324-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="b5324-331">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="b5324-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="b5324-332">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="b5324-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="b5324-333">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="b5324-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="b5324-334">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="b5324-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="b5324-335">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="b5324-336">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="b5324-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="b5324-337">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich `az login` mit dem Befehl an.</span><span class="sxs-lookup"><span data-stu-id="b5324-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="b5324-338">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, `{RESOURCE GROUP NAME}` wobei der Name der Ressourcengruppe für die neue Ressourcengruppe `{LOCATION}` und die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5324-339">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, `{KEY VAULT NAME}` wobei der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="b5324-340">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="b5324-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="b5324-341">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b5324-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="b5324-342">Hierarchische Werte (Konfigurations Abschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="b5324-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b5324-343">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="b5324-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="b5324-344">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="b5324-345">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b5324-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="b5324-346">Die Werte enthalten ein `_prod` Suffix, um Sie von den `_dev` suffixwerten zu unterscheiden, die in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="b5324-347">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="b5324-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="b5324-348">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="b5324-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="b5324-349">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="b5324-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="b5324-350">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="b5324-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="b5324-351">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="b5324-352">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b5324-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="b5324-353">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509 `#define` -Zertifikat, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Certificate`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="b5324-354">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="b5324-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="b5324-355">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="b5324-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="b5324-356">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="b5324-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="b5324-357">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="b5324-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="b5324-358">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="b5324-359">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="b5324-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="b5324-360">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="b5324-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="b5324-361">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="b5324-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="b5324-362">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="b5324-363">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="b5324-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="b5324-364">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="b5324-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="b5324-365">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="b5324-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="b5324-366">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der *appSettings. JSON* -Datei der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="b5324-367">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="b5324-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="b5324-368">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="b5324-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="b5324-369">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="b5324-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="b5324-370">Wählen Sie **Zugriffs Richtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="b5324-371">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="b5324-372">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="b5324-373">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="b5324-374">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b5324-374">Select **OK**.</span></span>
1. <span data-ttu-id="b5324-375">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-375">Select **Save**.</span></span>
1. <span data-ttu-id="b5324-376">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-376">Deploy the app.</span></span>

<span data-ttu-id="b5324-377">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte `IConfigurationRoot` aus dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="b5324-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="b5324-378">Nicht hierarchische Werte: der Wert für `SecretName` wird mit `config["SecretName"]`abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="b5324-379">Hierarchische Werte (Abschnitte): `:` verwenden Sie die Notation (Doppel `GetSection` Punkt) oder die Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b5324-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="b5324-380">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="b5324-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="b5324-381">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="b5324-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="b5324-382">Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der *appSettings. JSON* -Datei bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="b5324-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="b5324-383">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="b5324-383">Example values:</span></span>

* <span data-ttu-id="b5324-384">Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5324-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="b5324-385">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="b5324-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="b5324-386">Zertifikat Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="b5324-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="b5324-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5324-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="b5324-388">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5324-389">In der Entwicklungsumgebung werden geheime Werte mit dem `_dev` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="b5324-390">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="b5324-391">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5324-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="b5324-392">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b5324-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="b5324-393">In der Beispiel-App werden verwaltete Identitäten für Azure- `#define` Ressourcen verwendet, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Managed`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="b5324-394">Geben Sie den Tresor Namen in die Datei " *appSettings. JSON* " der APP ein.</span><span class="sxs-lookup"><span data-stu-id="b5324-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="b5324-395">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) `Managed` , wenn Sie auf die-Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="b5324-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="b5324-396">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei " *appSettings. JSON* " gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="b5324-397">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="b5324-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="b5324-398">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="b5324-399">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="b5324-400">Die Objekt-ID wird im Azure-Portal im **Identitäts** Bereich der APP Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="b5324-401">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen `list` Sie `get` der APP die Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="b5324-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="b5324-402">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="b5324-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="b5324-403">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="b5324-403">The sample app:</span></span>

* <span data-ttu-id="b5324-404">Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="b5324-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="b5324-405">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="b5324-406">Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5324-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="b5324-407">Die <xref:Microsoft.Azure.KeyVault.KeyVaultClient> -Instanz wird mit einer Standard Implementierung von <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> verwendet, die alle geheimen Werte lädt und doppelte Bindestriche (`--`) durch Doppelpunkte (`:`) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="b5324-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="b5324-408">Beispiel Wert für Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="b5324-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="b5324-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b5324-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="b5324-410">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b5324-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="b5324-411">In der Entwicklungsumgebung verfügen geheime Werte über das `_dev` -Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="b5324-412">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="b5324-413">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="b5324-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="b5324-414">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="b5324-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="b5324-415">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="b5324-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="b5324-416">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="b5324-416">Use a key name prefix</span></span>

<span data-ttu-id="b5324-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>stellt eine Überladung bereit, die eine <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Implementierung von akzeptiert, mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="b5324-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="b5324-418">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="b5324-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="b5324-419">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="b5324-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b5324-420">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="b5324-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="b5324-421">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="b5324-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="b5324-422">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="b5324-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="b5324-423">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="b5324-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="b5324-424">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) `5100-AppSecret`für hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b5324-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="b5324-425">Jede APP-Version lädt den Wert für `AppSecret`die Versionierung mit Versions Angabe in die Konfiguration, wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="b5324-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem benutzerdefinierten <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="b5324-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="b5324-427">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="b5324-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="b5324-428">`Load`lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="b5324-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="b5324-429">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="b5324-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="b5324-430">`GetKey`:</span></span>
  * <span data-ttu-id="b5324-431">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="b5324-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="b5324-432">Ersetzt zwei Bindestriche in einem beliebigen Namen durch `KeyDelimiter`das-Zeichen, das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="b5324-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="b5324-433">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="b5324-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="b5324-434">Die `Load` -Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="b5324-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="b5324-435">Wenn ein Versions Präfix mit `Load`gefunden wird, verwendet der Algorithmus `GetKey` die-Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="b5324-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="b5324-436">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="b5324-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="b5324-437">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="b5324-438">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="b5324-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="b5324-439">Im folgenden Beispiel wird die App-Version auf `5.0.0.0`festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b5324-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b5324-440">Vergewissern Sie sich `<UserSecretsId>` , dass eine Eigenschaft in der Projektdatei der app vorhanden `{GUID}` ist, wobei eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="b5324-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="b5324-441">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b5324-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="b5324-442">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="b5324-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="b5324-443">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="b5324-444">Der Zeichen folgen Schlüssel `5000-AppSecret` für wird mit der in der Projektdatei der APP (`5.0.0.0`) angegebenen Version der APP abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="b5324-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="b5324-445">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="b5324-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="b5324-446">Während der gesamten APP wird beim Lesen der Konfiguration `AppSecret` mit dem Schlüssel der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="b5324-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="b5324-447">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird `5.1.0.0_secret_value_dev` der geheime Wert in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktionsumgebung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b5324-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="b5324-448">Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="b5324-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="b5324-449">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="b5324-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b5324-450">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="b5324-450">Bind an array to a class</span></span>

<span data-ttu-id="b5324-451">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="b5324-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="b5324-452">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen (`:`Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden`:0:`, `:1:`die &hellip; `:{n}:`ein Array bilden (,,).</span><span class="sxs-lookup"><span data-stu-id="b5324-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="b5324-453">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="b5324-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="b5324-454">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="b5324-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="b5324-455">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche (`--`) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="b5324-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="b5324-456">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten`--0--`(, `--1--`, &hellip; `--{n}--`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b5324-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="b5324-457">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="b5324-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="b5324-458">Im `WriteTo` Array sind zwei Objektliterale definiert, die zwei serilog- *senken*reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="b5324-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="b5324-459">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von`--`Double Dash ()-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="b5324-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="b5324-460">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="b5324-460">Key</span></span> | <span data-ttu-id="b5324-461">Wert</span><span class="sxs-lookup"><span data-stu-id="b5324-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="b5324-462">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="b5324-462">Reload secrets</span></span>

<span data-ttu-id="b5324-463">Geheimnisse werden zwischen `IConfigurationRoot.Reload()` gespeichert, bis aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="b5324-464">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst `Reload` berücksichtigt, wenn der ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b5324-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="b5324-465">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="b5324-465">Disabled and expired secrets</span></span>

<span data-ttu-id="b5324-466">Deaktivierte und abgelaufene geheim <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>nisse lösen einen aus.</span><span class="sxs-lookup"><span data-stu-id="b5324-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="b5324-467">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="b5324-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b5324-468">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b5324-468">Troubleshoot</span></span>

<span data-ttu-id="b5324-469">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b5324-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b5324-470">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="b5324-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="b5324-471">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b5324-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="b5324-472">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="b5324-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="b5324-473">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b5324-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="b5324-474">Die Zugriffs Richtlinie enthält `Get` nicht die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="b5324-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="b5324-475">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="b5324-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="b5324-476">Die APP hat den falschen Schlüssel Tresor Namen (`KeyVaultName`), Azure AD Anwendungs-ID`AzureADApplicationId`() oder Azure AD Zertifikat Fingerabdruck (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="b5324-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="b5324-477">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="b5324-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="b5324-478">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b5324-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5324-479">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5324-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b5324-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="b5324-481">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="b5324-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="b5324-482">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b5324-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="b5324-483">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="b5324-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="b5324-484">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="b5324-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="b5324-485">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="b5324-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

