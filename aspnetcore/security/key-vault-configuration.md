---
title: Azure Key Vault Konfigurations Anbieters in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie mit dem Azure Key Vault-Konfigurations Anbieter eine App mithilfe von Name-Wert-Paaren konfigurieren, die zur Laufzeit geladen werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/01/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 0d0b6e20a1901d4a2630ce263b5fd0cd7bcca8fe
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776650"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="16051-103">Azure Key Vault Konfigurations Anbieters in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="16051-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="16051-104">Von [Luke Latham](https://github.com/guardrex) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="16051-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="16051-105">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="16051-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="16051-106">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="16051-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="16051-107">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="16051-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="16051-108">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="16051-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="16051-109">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="16051-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="16051-110">Dieses Szenario ist für apps verfügbar, die auf ASP.net Core 2,1 oder höher ausgerichtet sind.</span><span class="sxs-lookup"><span data-stu-id="16051-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="16051-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16051-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="16051-112">Pakete</span><span class="sxs-lookup"><span data-stu-id="16051-112">Packages</span></span>

<span data-ttu-id="16051-113">Fügen Sie dem Paket " [Microsoft. Extensions. Configuration. azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) " einen Paket Verweis hinzu, um den Azure Key Vault-Konfigurations Anbieter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="16051-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="16051-114">Um das Szenario " [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) " zu übernehmen, fügen Sie dem Paket " [Microsoft. Azure. Services. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="16051-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="16051-115">Zum Zeitpunkt des Schreibens bietet die neueste stabile Version von `Microsoft.Azure.Services.AppAuthentication`, Version `1.0.3`, Unterstützung für vom [System zugewiesene verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span><span class="sxs-lookup"><span data-stu-id="16051-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="16051-116">Die Unterstützung für vom `1.2.0-preview2` *Benutzer zugewiesene verwaltete Identitäten* ist im Paket verfügbar.</span><span class="sxs-lookup"><span data-stu-id="16051-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="16051-117">In diesem Thema wird die Verwendung von vom System verwalteten Identitäten veranschaulicht, und die bereitgestellte Beispiel `1.0.3` -APP `Microsoft.Azure.Services.AppAuthentication` verwendet die Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="16051-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="16051-118">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="16051-118">Sample app</span></span>

<span data-ttu-id="16051-119">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, `#define` die durch die-Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="16051-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="16051-120">`Certificate`&ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="16051-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="16051-121">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="16051-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="16051-122">`Managed`Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind. &ndash;</span><span class="sxs-lookup"><span data-stu-id="16051-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="16051-123">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="16051-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="16051-124">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="16051-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="16051-125">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="16051-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="16051-126">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven`#define`() finden <xref:index#preprocessor-directives-in-sample-code>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="16051-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="16051-127">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="16051-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="16051-128">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="16051-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="16051-129">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Speicher des geheimen Haupt Schlüssels geladen.</span><span class="sxs-lookup"><span data-stu-id="16051-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="16051-130">Das Secret Manager-Tool erfordert `<UserSecretsId>` eine Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="16051-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="16051-131">Legen Sie den-Eigenschafts Wert (`{GUID}`) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="16051-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="16051-132">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="16051-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="16051-133">Hierarchische Werte (Konfigurations Abschnitte) verwenden `:` einen (Doppelpunkt) als Trennzeichen in ASP.net Core Namen von [Konfigurations](xref:fundamentals/configuration/index) Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="16051-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="16051-134">Der geheime Manager wird von einer Befehlsshell verwendet, die für den Inhalts Stamm des Projekts `{SECRET NAME}` geöffnet ist, wobei `{SECRET VALUE}` der Name und der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="16051-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="16051-135">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem Inhalts Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="16051-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="16051-136">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` `_prod`Suffix in geändert.</span><span class="sxs-lookup"><span data-stu-id="16051-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="16051-137">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="16051-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="16051-138">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16051-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="16051-139">Die Anweisungen [im Schnellstart: Festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="16051-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="16051-140">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="16051-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="16051-141">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="16051-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="16051-142">Klicken Sie in der oberen rechten Ecke eines Code Blocks auf **ausprobieren** .</span><span class="sxs-lookup"><span data-stu-id="16051-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="16051-143">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="16051-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="16051-144">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="16051-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="16051-145">Wählen Sie im Menü in der oberen rechten Ecke des Azure-Portal die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="16051-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="16051-146">Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) und [unter Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="16051-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="16051-147">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich `az login` mit dem Befehl an.</span><span class="sxs-lookup"><span data-stu-id="16051-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="16051-148">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, `{RESOURCE GROUP NAME}` wobei der Name der Ressourcengruppe für die neue Ressourcengruppe `{LOCATION}` und die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="16051-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="16051-149">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, `{KEY VAULT NAME}` wobei der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="16051-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="16051-150">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="16051-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="16051-151">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="16051-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="16051-152">Hierarchische Werte (Konfigurations Abschnitte) `--` verwenden (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="16051-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="16051-153">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="16051-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="16051-154">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="16051-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="16051-155">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="16051-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="16051-156">Die Werte enthalten ein `_prod` Suffix, um Sie von den `_dev` suffixwerten zu unterscheiden, die in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="16051-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="16051-157">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="16051-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="16051-158">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="16051-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="16051-159">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="16051-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="16051-160">Weitere Informationen finden Sie unter Informationen [zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="16051-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="16051-161">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="16051-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="16051-162">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="16051-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="16051-163">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509 `#define` -Zertifikat, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Certificate`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="16051-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="16051-164">Erstellen Sie ein PKCS # 12-Archiv Zertifikat ( *. pfx*).</span><span class="sxs-lookup"><span data-stu-id="16051-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="16051-165">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="16051-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="16051-166">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="16051-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="16051-167">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="16051-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="16051-168">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="16051-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="16051-169">Exportieren Sie das PKCS # 12-Archiv Zertifikat ( *. pfx*) als ein-codiertes Zertifikat ( *. CER*).</span><span class="sxs-lookup"><span data-stu-id="16051-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="16051-170">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="16051-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="16051-171">Laden Sie das der-codierte Zertifikat ( *. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="16051-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="16051-172">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="16051-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="16051-173">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="16051-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="16051-174">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="16051-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="16051-175">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="16051-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="16051-176">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der *appSettings. JSON* -Datei der app.</span><span class="sxs-lookup"><span data-stu-id="16051-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="16051-177">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="16051-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="16051-178">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="16051-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="16051-179">Wählen Sie **Zugriffsrichtlinien**aus.</span><span class="sxs-lookup"><span data-stu-id="16051-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="16051-180">Wählen Sie **neue hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="16051-180">Select **Add new**.</span></span>
1. <span data-ttu-id="16051-181">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="16051-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="16051-182">Wählen Sie die Schaltfläche **auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="16051-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="16051-183">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="16051-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="16051-184">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="16051-184">Select **OK**.</span></span>
1. <span data-ttu-id="16051-185">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="16051-185">Select **Save**.</span></span>
1. <span data-ttu-id="16051-186">Stellen Sie die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="16051-186">Deploy the app.</span></span>

<span data-ttu-id="16051-187">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte `IConfigurationRoot` aus dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="16051-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="16051-188">Nicht hierarchische Werte: Der Wert für `SecretName` wird mit `config["SecretName"]`abgerufen.</span><span class="sxs-lookup"><span data-stu-id="16051-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="16051-189">Hierarchische Werte (Abschnitte): Verwenden `:` Sie die Notation (Doppelpunkt `GetSection` ) oder die Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="16051-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="16051-190">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="16051-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="16051-191">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="16051-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="16051-192">Die App Ruft `AddAzureKeyVault` mit Werten auf, die von der *appSettings. JSON* -Datei bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="16051-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="16051-193">Beispiel Werte:</span><span class="sxs-lookup"><span data-stu-id="16051-193">Example values:</span></span>

* <span data-ttu-id="16051-194">Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="16051-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="16051-195">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="16051-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="16051-196">Zertifikat Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="16051-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="16051-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="16051-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="16051-198">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="16051-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="16051-199">In der Entwicklungsumgebung werden geheime Werte mit dem `_dev` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="16051-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="16051-200">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="16051-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="16051-201">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="16051-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="16051-202">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault mithilfe Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) zu authentifizieren. in der APP gespeichert.</span><span class="sxs-lookup"><span data-stu-id="16051-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="16051-203">In der Beispiel-App werden verwaltete Identitäten für Azure- `#define` Ressourcen verwendet, wenn die-Anweisung am Anfang der *Program.cs* -Datei auf `Managed`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="16051-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="16051-204">Geben Sie den Tresor Namen in die Datei " *appSettings. JSON* " der APP ein.</span><span class="sxs-lookup"><span data-stu-id="16051-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="16051-205">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) `Managed` , wenn Sie auf die-Version festgelegt ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="16051-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="16051-206">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei " *appSettings. JSON* " gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="16051-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="16051-207">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="16051-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="16051-208">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="16051-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="16051-209">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="16051-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="16051-210">Die Objekt-ID wird im Azure-Portal im **Identitäts** Bereich der APP Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="16051-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="16051-211">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen `list` Sie `get` der APP die Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="16051-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="16051-212">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="16051-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="16051-213">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="16051-213">The sample app:</span></span>

* <span data-ttu-id="16051-214">Erstellt eine Instanz der `AzureServiceTokenProvider` -Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="16051-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="16051-215">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="16051-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="16051-216">Ein neues `KeyVaultClient` wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="16051-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="16051-217">Die `KeyVaultClient` -Instanz wird mit einer Standard Implementierung von `IKeyVaultSecretManager` verwendet, die alle geheimen Werte lädt und doppelte Bindestriche (`--`) durch`:`Doppelpunkte () in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="16051-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="16051-218">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="16051-218">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="16051-219">In der Entwicklungsumgebung verfügen geheime Werte über das `_dev` -Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="16051-219">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="16051-220">In der Produktionsumgebung werden die Werte mit dem `_prod` -Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="16051-220">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="16051-221">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="16051-221">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="16051-222">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="16051-222">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="16051-223">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="16051-223">Use a key name prefix</span></span>

<span data-ttu-id="16051-224">`AddAzureKeyVault`stellt eine Überladung bereit, die eine `IKeyVaultSecretManager`Implementierung von akzeptiert, mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="16051-224">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="16051-225">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="16051-225">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="16051-226">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="16051-226">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="16051-227">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="16051-227">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="16051-228">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="16051-228">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="16051-229">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="16051-229">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="16051-230">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="16051-230">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="16051-231">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) `5100-AppSecret`für hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="16051-231">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="16051-232">Jede APP-Version lädt den Wert für `AppSecret`die Versionierung mit Versions Angabe in die Konfiguration, wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="16051-232">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="16051-233">`AddAzureKeyVault`wird mit einem benutzerdefinierten `IKeyVaultSecretManager`aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="16051-233">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="16051-234">Die `IKeyVaultSecretManager` Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="16051-234">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="16051-235">Die `Load` -Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="16051-235">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="16051-236">Wenn ein Versions Präfix mit `Load`gefunden wird, verwendet der Algorithmus die `GetKey` -Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="16051-236">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="16051-237">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="16051-237">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="16051-238">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="16051-238">When this approach is implemented:</span></span>

1. <span data-ttu-id="16051-239">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="16051-239">The app's version specified in the app's project file.</span></span> <span data-ttu-id="16051-240">Im folgenden Beispiel wird die App-Version auf `5.0.0.0`festgelegt:</span><span class="sxs-lookup"><span data-stu-id="16051-240">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="16051-241">Vergewissern Sie sich `<UserSecretsId>` , dass eine Eigenschaft in der Projektdatei der app vorhanden `{GUID}` ist, wobei eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="16051-241">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="16051-242">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="16051-242">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="16051-243">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="16051-243">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="16051-244">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="16051-244">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="16051-245">Der Zeichen folgen Schlüssel `5000-AppSecret` für wird mit der in der Projektdatei der APP (`5.0.0.0`) angegebenen Version der APP abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="16051-245">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="16051-246">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="16051-246">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="16051-247">Während der gesamten APP wird beim Lesen der Konfiguration `AppSecret` mit dem Schlüssel der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="16051-247">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="16051-248">Wenn die App-Version in der Projektdatei `5.1.0.0` in geändert wird und die APP erneut ausgeführt wird, wird `5.1.0.0_secret_value_dev` der geheime Wert in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktionsumgebung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="16051-248">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="16051-249">Sie können auch eine eigene `KeyVaultClient` Implementierung für `AddAzureKeyVault`bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="16051-249">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="16051-250">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="16051-250">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="16051-251">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="16051-251">Bind an array to a class</span></span>

<span data-ttu-id="16051-252">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="16051-252">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="16051-253">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen (`:`Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden`:0:`, `:1:`die ein Array bilden (,,...</span><span class="sxs-lookup"><span data-stu-id="16051-253">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="16051-254">`:{n}:`) angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="16051-254">`:{n}:`).</span></span> <span data-ttu-id="16051-255">Weitere Informationen finden [Sie unter Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="16051-255">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="16051-256">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="16051-256">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="16051-257">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche (`--`) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="16051-257">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="16051-258">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten`--0--`(, `--1--`, &hellip; `--{n}--`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="16051-258">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="16051-259">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="16051-259">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="16051-260">Im `WriteTo` Array sind zwei Objektliterale definiert, die zwei serilog- *senken*reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="16051-260">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="16051-261">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von`--`Double Dash ()-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="16051-261">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="16051-262">Key</span><span class="sxs-lookup"><span data-stu-id="16051-262">Key</span></span> | <span data-ttu-id="16051-263">Wert</span><span class="sxs-lookup"><span data-stu-id="16051-263">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="16051-264">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="16051-264">Reload secrets</span></span>

<span data-ttu-id="16051-265">Geheimnisse werden zwischen `IConfigurationRoot.Reload()` gespeichert, bis aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="16051-265">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="16051-266">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst `Reload` berücksichtigt, wenn der ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="16051-266">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="16051-267">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="16051-267">Disabled and expired secrets</span></span>

<span data-ttu-id="16051-268">Deaktivierte und abgelaufene geheim `KeyVaultClientException` nisse lösen zur Laufzeit eine aus.</span><span class="sxs-lookup"><span data-stu-id="16051-268">Disabled and expired secrets throw a `KeyVaultClientException` at runtime.</span></span> <span data-ttu-id="16051-269">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="16051-269">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="16051-270">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="16051-270">Troubleshoot</span></span>

<span data-ttu-id="16051-271">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="16051-271">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="16051-272">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="16051-272">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="16051-273">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="16051-273">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="16051-274">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="16051-274">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="16051-275">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="16051-275">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="16051-276">Die Zugriffs Richtlinie enthält `Get` nicht die Berechtigungen und. `List`</span><span class="sxs-lookup"><span data-stu-id="16051-276">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="16051-277">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="16051-277">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="16051-278">Die APP hat den falschen Schlüssel Tresor Namen (`KeyVaultName`), Azure AD Anwendungs-ID`AzureADApplicationId`() oder Azure AD Zertifikat Fingerabdruck (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="16051-278">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="16051-279">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="16051-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16051-280">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="16051-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="16051-281">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="16051-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="16051-282">Microsoft Azure: Dokumentation zu Key Vault</span><span class="sxs-lookup"><span data-stu-id="16051-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="16051-283">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="16051-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="16051-284">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="16051-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="16051-285">Schnellstart: Festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe einer .net-Web-App</span><span class="sxs-lookup"><span data-stu-id="16051-285">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="16051-286">Tutorial: Verwenden von Azure Key Vault mit Azure Windows Virtual Machine in .net</span><span class="sxs-lookup"><span data-stu-id="16051-286">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
