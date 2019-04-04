---
title: Azure Key Vault-Konfigurationsanbieter in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie mit der Azure Key Vault-Konfigurationsanbieter, so konfigurieren Sie eine app mithilfe von Name / Wert-Paaren, die zur Laufzeit geladen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 8fd1cca1803d3f1d44d80ec63c5cfc259cbdaf55
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012694"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="6c381-103">Azure Key Vault-Konfigurationsanbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c381-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="6c381-104">Durch [Luke Latham](https://github.com/guardrex) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6c381-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="6c381-105">In diesem Dokument wird erläutert, wie Sie mit der [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Konfigurationsanbieter zum Laden von app-Konfigurationswerte aus Azure Key Vault-Geheimnissen.</span><span class="sxs-lookup"><span data-stu-id="6c381-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="6c381-106">Azure Key Vault ist ein cloudbasierter Dienst, die hilft beim Schutz von kryptografischen Schlüsseln und Geheimnissen, die von apps und-Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="6c381-107">Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-apps:</span><span class="sxs-lookup"><span data-stu-id="6c381-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="6c381-108">Steuern des Zugriffs auf sensible Konfigurationsdaten aus.</span><span class="sxs-lookup"><span data-stu-id="6c381-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="6c381-109">140-2 Level 2, die erfüllt die Anforderungen für FIPS überprüft die von Hardwaresicherheitsmodulen (HSM), wenn Konfigurationsdaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="6c381-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="6c381-110">Dieses Szenario ist für apps mit ASP.NET Core 2.1 oder höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6c381-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="6c381-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6c381-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="6c381-112">Pakete</span><span class="sxs-lookup"><span data-stu-id="6c381-112">Packages</span></span>

<span data-ttu-id="6c381-113">Um die Azure Key Vault-Konfigurationsanbieter zu verwenden, fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Paket.</span><span class="sxs-lookup"><span data-stu-id="6c381-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="6c381-114">Übernehmen der [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) Szenario fügen Sie einen Paketverweis auf die [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) Paket.</span><span class="sxs-lookup"><span data-stu-id="6c381-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="6c381-115">Zum Zeitpunkt der schreiben, der neuesten stabile Version des `Microsoft.Azure.Services.AppAuthentication`, Version `1.0.3`, bietet Unterstützung für [vom System zugewiesene verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="6c381-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="6c381-116">Unterstützung für *Benutzer zugewiesene Identitäten verwaltet* finden Sie in der `1.2.0-preview2` Paket.</span><span class="sxs-lookup"><span data-stu-id="6c381-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="6c381-117">In diesem Thema veranschaulicht die Verwendung von vom System verwaltet Identitäten und die bereitgestellten Beispiel-app verwendet Version `1.0.3` von der `Microsoft.Azure.Services.AppAuthentication` Paket.</span><span class="sxs-lookup"><span data-stu-id="6c381-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="6c381-118">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="6c381-118">Sample app</span></span>

<span data-ttu-id="6c381-119">Die Beispiel-app ausgeführt wird, in einem von zwei Modi, die bestimmt, indem die `#define` Anweisung am Anfang der *"Program.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="6c381-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* `Certificate` <span data-ttu-id="6c381-120">&ndash; Veranschaulicht die Verwendung eines Azure Key Vault-Client-ID und x. 509-Zertifikats auf Access-Geheimnisse in Azure Key Vault gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6c381-120">&ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="6c381-121">Diese Version des Beispiels kann von jedem Standort und Azure App Service oder einem beliebigen Host können ASP.NET Core-Apps bereitgestellt, ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* `Managed` <span data-ttu-id="6c381-122">&ndash; Veranschaulicht, wie [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) zum Authentifizieren von der app in Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen, die in der app Code oder Konfiguration gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6c381-122">&ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="6c381-123">Verwendung von verwalteten Identitäten um zu authentifizieren, sind eine Azure AD-Anwendungs-ID und Kennwort (geheimer Clientschlüssel) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6c381-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="6c381-124">Die `Managed` -Version des Beispiels in Azure bereitgestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="6c381-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="6c381-125">Befolgen Sie die Anweisungen in der [verwenden Sie die verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="6c381-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="6c381-126">Weitere Informationen zum Konfigurieren einer Beispiel-app mittels präprozessoranweisungen (`#define`), finden Sie unter <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="6c381-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="6c381-127">Geheimen Speicher in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="6c381-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="6c381-128">Legen Sie die Geheimnisse, die lokal mithilfe der [Secret Manager-Tool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6c381-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="6c381-129">Geheimnisse werden geladen, wenn die Beispiel-app auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, aus dem lokalen Speicher für die Geheimnis-Manager.</span><span class="sxs-lookup"><span data-stu-id="6c381-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="6c381-130">Das Geheimnis-Manager-Tool erfordert eine `<UserSecretsId>` -Eigenschaft in der app-Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="6c381-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="6c381-131">Legen Sie den Wert der Eigenschaft (`{GUID}`) auf eine beliebige eindeutige GUID:</span><span class="sxs-lookup"><span data-stu-id="6c381-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="6c381-132">Geheime Schlüssel werden als Name / Wert-Paaren erstellt.</span><span class="sxs-lookup"><span data-stu-id="6c381-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="6c381-133">Verwenden von hierarchische Werten (Konfigurationsabschnitte) eine `:` (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index) Schlüssel Namen.</span><span class="sxs-lookup"><span data-stu-id="6c381-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="6c381-134">Der Geheimnis-Manager wird verwendet, über eine Befehlsshell, die für das inhaltsstammverzeichnis des Projekts geöffnet, in denen `{SECRET NAME}` ist der Name und `{SECRET VALUE}` ist der Wert:</span><span class="sxs-lookup"><span data-stu-id="6c381-134">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="6c381-135">Führen Sie die folgenden Befehle in einer Befehlsshell aus Inhalt Stamm des Projekts, um die geheimen Schlüssel für die Beispiel-app festzulegen:</span><span class="sxs-lookup"><span data-stu-id="6c381-135">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="6c381-136">Wenn diese vertraulichen Informationen befinden sich in Azure Key Vault in der [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt der `_dev` Suffix wird geändert, um `_prod`.</span><span class="sxs-lookup"><span data-stu-id="6c381-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="6c381-137">Das Suffix bietet einen visuellen Hinweis in der Ausgabe der Anwendung, der angibt, der Quelle der Konfigurationswerte an.</span><span class="sxs-lookup"><span data-stu-id="6c381-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="6c381-138">Geheimen Speicher in der produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6c381-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="6c381-139">Die Anweisungen unter dem [Schnellstart: Festlegen und Abrufen ein geheimen Schlüssels aus Azure Key Vault mithilfe der Azure CLI](/azure/key-vault/quick-create-cli) Thema werden hier zusammengefasst, für das Erstellen von Azure Key Vault, und Speichern von Geheimnissen, die von der Beispiel-app verwendet.</span><span class="sxs-lookup"><span data-stu-id="6c381-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="6c381-140">Das Thema für Weitere Informationen finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="6c381-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="6c381-141">Öffnen mit einer der folgenden Methoden in Azure-Cloud-Shell die [Azure-Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="6c381-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="6c381-142">Wählen Sie **ausprobieren** in der oberen rechten Ecke eines Codeblocks.</span><span class="sxs-lookup"><span data-stu-id="6c381-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="6c381-143">Verwenden Sie die Zeichenfolge "Azure-CLI" in das Textfeld ein.</span><span class="sxs-lookup"><span data-stu-id="6c381-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="6c381-144">Öffnen Sie Cloud Shell in Ihrem Browser mit der **Cloud Shell starten** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="6c381-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="6c381-145">Wählen Sie die **Cloud Shell** im auf die Schaltfläche in der oberen rechten Ecke des Azure-Portals.</span><span class="sxs-lookup"><span data-stu-id="6c381-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="6c381-146">Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="6c381-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="6c381-147">Wenn Sie bereits authentifiziert sind nicht, melden Sie sich mit den `az login` Befehl.</span><span class="sxs-lookup"><span data-stu-id="6c381-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="6c381-148">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, in denen `{RESOURCE GROUP NAME}` ist der Ressourcengruppenname für die neue Ressourcengruppe und `{LOCATION}` ist die Azure-Region (Rechenzentrum):</span><span class="sxs-lookup"><span data-stu-id="6c381-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6c381-149">Erstellen eines schlüsseltresors in der Ressourcengruppe mit dem folgenden Befehl, in denen `{KEY VAULT NAME}` ist der Name für die neue Key Vault und `{LOCATION}` ist die Azure-Region (Rechenzentrum):</span><span class="sxs-lookup"><span data-stu-id="6c381-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="6c381-150">Erstellen Sie Geheimnisse im schlüsseltresor als Name / Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="6c381-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="6c381-151">Geheime Azure Key Vault-Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="6c381-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="6c381-152">Verwenden von hierarchische Werten (Konfigurationsabschnitte) `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="6c381-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="6c381-153">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt über einen Unterschlüssel in zu begrenzen [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index), sind nicht zulässig in Key Vault-Instanz den geheimen Namen.</span><span class="sxs-lookup"><span data-stu-id="6c381-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="6c381-154">Daher sind die beiden Striche verwendet und für einen Doppelpunkt ausgetauscht werden, wenn die geheimen Schlüssel in der app Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="6c381-155">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="6c381-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="6c381-156">Die Werte umfassen eine `_prod` Suffix zur Unterscheidung von den `_dev` suffix Werte in der Entwicklungsumgebung aus geheime Benutzerschlüssel.</span><span class="sxs-lookup"><span data-stu-id="6c381-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="6c381-157">Ersetzen Sie dies `{KEY VAULT NAME}` durch den Namen des schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="6c381-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a><span data-ttu-id="6c381-158">Verwenden Sie Anwendungs-ID und geheimer Clientschlüssel für nicht-Azure gehosteten apps</span><span class="sxs-lookup"><span data-stu-id="6c381-158">Use Application ID and Client Secret for non-Azure-hosted apps</span></span>

<span data-ttu-id="6c381-159">Konfigurieren von Azure AD, Azure Key Vault und die app mit einer Azure Active Directory-Anwendungs-ID und das x. 509-Zertifikat zur Authentifizierung in einen schlüsseltresor **Wenn die app gehostet wird, außerhalb von Azure**.</span><span class="sxs-lookup"><span data-stu-id="6c381-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="6c381-160">Weitere Informationen finden Sie unter [über Schlüssel, Geheimnisse und Zertifikate](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="6c381-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="6c381-161">Mit einem Anwendungs-ID und x. 509-Zertifikat für apps, die in Azure gehostet wird zwar unterstützt, es wird empfohlen, [verwaltet Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer app in Azure.</span><span class="sxs-lookup"><span data-stu-id="6c381-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="6c381-162">Speichern ein Zertifikat aus, in der app oder in der Entwicklungsumgebung ist keine verwaltete Identitäten erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6c381-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="6c381-163">Die beispielanwendung verwendet ein Anwendungs-ID und das x. 509-Zertifikat bei der `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="6c381-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="6c381-164">Registrieren einer app in Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="6c381-164">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="6c381-165">Laden Sie den öffentlichen Schlüssel hoch:</span><span class="sxs-lookup"><span data-stu-id="6c381-165">Upload the public key:</span></span>
   1. <span data-ttu-id="6c381-166">Wählen Sie die app in Azure AD.</span><span class="sxs-lookup"><span data-stu-id="6c381-166">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="6c381-167">Navigieren Sie zu **Einstellungen** > **Schlüssel**.</span><span class="sxs-lookup"><span data-stu-id="6c381-167">Navigate to **Settings** > **Keys**.</span></span>
   1. <span data-ttu-id="6c381-168">Wählen Sie **öffentlichen Schlüssel hochladen** zum Hochladen des Zertifikats, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="6c381-168">Select **Upload Public Key** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="6c381-169">Zusätzlich zur Verwendung einer *CER*, *PEM*, oder *CRT* Zertifikat eine *PFX* Zertifikat hochgeladen werden kann.</span><span class="sxs-lookup"><span data-stu-id="6c381-169">In addition to using a *.cer*, *.pem*, or *.crt* certificate, a *.pfx* certificate can be uploaded.</span></span>
1. <span data-ttu-id="6c381-170">Der Name des schlüsseltresors und den Anwendungs-ID der App Store *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="6c381-170">Store the key vault name and Application ID in the app's *appsettings.json* file.</span></span> <span data-ttu-id="6c381-171">Platzieren Sie das Zertifikat im Stammverzeichnis der app oder in der app-Zertifikatspeicher&dagger;.</span><span class="sxs-lookup"><span data-stu-id="6c381-171">Place the certificate at the root of the app or in the app's certificate store&dagger;.</span></span>
1. <span data-ttu-id="6c381-172">Navigieren Sie zu **Schlüsseltresore** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="6c381-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="6c381-173">Wählen Sie die Key Vault-Instanz, die Sie in erstellt die [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="6c381-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="6c381-174">Wählen Sie **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="6c381-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="6c381-175">Wählen Sie **neu hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="6c381-175">Select **Add new**.</span></span>
1. <span data-ttu-id="6c381-176">Wählen Sie **Prinzipal auswählen** , und wählen Sie nach dem Namen die registrierte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="6c381-176">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="6c381-177">Wählen Sie die **wählen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="6c381-177">Select the **Select** button.</span></span>
1. <span data-ttu-id="6c381-178">Open **Berechtigungen für Geheimnis** , und geben Sie die app mit **erhalten** und **Liste** Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="6c381-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="6c381-179">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6c381-179">Select **OK**.</span></span>
1. <span data-ttu-id="6c381-180">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="6c381-180">Select **Save**.</span></span>
1. <span data-ttu-id="6c381-181">Bereitstellen der app an.</span><span class="sxs-lookup"><span data-stu-id="6c381-181">Deploy the app.</span></span>

<span data-ttu-id="6c381-182">&dagger;In der Beispiel-app das Zertifikat durch Erstellen eines neuen direkt über die physischen Zertifikatdatei in das Stammverzeichnis der app verbraucht `X509Certificate2` beim Aufrufen von `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="6c381-182">&dagger;In the sample app, the certificate is consumed directly from the physical certificate file in the root of the app by creating a new `X509Certificate2` when calling `AddAzureKeyVault`.</span></span> <span data-ttu-id="6c381-183">Ein alternativer Ansatz ist das Betriebssystem das Zertifikat verwalten kann.</span><span class="sxs-lookup"><span data-stu-id="6c381-183">An alternative approach is to allow the OS to manage the certificate.</span></span> <span data-ttu-id="6c381-184">Weitere Informationen finden Sie unter den [ermöglichen dem Betriebssystem das x. 509-Zertifikat verwalten](#allow-the-os-to-manage-the-x509-certificate) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="6c381-184">For more information, see the [Allow the OS to manage the X.509 certificate](#allow-the-os-to-manage-the-x509-certificate) section.</span></span>

<span data-ttu-id="6c381-185">Die `Certificate` Beispiel-app erhält die Konfigurationswerte aus `IConfigurationRoot` mit dem gleichen Namen wie den Namen des geheimen Schlüssels:</span><span class="sxs-lookup"><span data-stu-id="6c381-185">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="6c381-186">Nicht-hierarchischen Werten: Der Wert für `SecretName` wird abgerufen, mit `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="6c381-186">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="6c381-187">Hierarchische Werte (Abschnitte): Verwendung `:` (Doppelpunkt)-Notation oder der `GetSection` -Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="6c381-187">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="6c381-188">Verwenden Sie einen dieser Ansätze, um den Konfigurationswert zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="6c381-188">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="6c381-189">Die app ruft `AddAzureKeyVault` mit Werten, die vom der *"appSettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="6c381-189">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=12-15)]

<span data-ttu-id="6c381-190">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="6c381-190">Example values:</span></span>

* <span data-ttu-id="6c381-191">Name des schlüsseltresors:</span><span class="sxs-lookup"><span data-stu-id="6c381-191">Key vault name:</span></span> `contosovault`
* <span data-ttu-id="6c381-192">Anwendungs-ID:</span><span class="sxs-lookup"><span data-stu-id="6c381-192">Application ID:</span></span> `627e911e-43cc-61d4-992e-12db9c81b413`

<span data-ttu-id="6c381-193">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6c381-193">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="6c381-194">Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen.</span><span class="sxs-lookup"><span data-stu-id="6c381-194">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6c381-195">In der Entwicklungsumgebung, laden Sie geheime Werte mit den `_dev` Suffix.</span><span class="sxs-lookup"><span data-stu-id="6c381-195">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="6c381-196">In der produktionsumgebung, laden Sie die Werte mit den `_prod` Suffix.</span><span class="sxs-lookup"><span data-stu-id="6c381-196">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="6c381-197">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6c381-197">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="6c381-198">**Eine app in Azure bereitgestellten** profitieren von [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview), die die app für die Authentifizierung mit Azure Key Vault ermöglicht die Verwendung von Azure AD-Authentifizierung ohne Anmeldeinformationen (Anwendungs-ID und Password/Client Geheimnis) in der app gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6c381-198">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="6c381-199">Die Beispiel-app verwaltete Identitäten verwendet, für die Azure-Ressourcen bei der `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Managed`.</span><span class="sxs-lookup"><span data-stu-id="6c381-199">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="6c381-200">Geben Sie den Namen des schlüsseltresors in der app *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="6c381-200">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="6c381-201">Die Beispiel-app nicht erforderlich, eine Anwendungs-ID und Kennwort (geheimer Clientschlüssel) bei Festlegung auf den `Managed` Version, sodass Sie die Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="6c381-201">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="6c381-202">Die app in Azure bereitgestellt wird, und Azure authentifiziert die app für den Zugriff auf Azure Key Vault verwenden nur den Namen des schlüsseltresors in gespeicherten die *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="6c381-202">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="6c381-203">Stellen Sie die Beispiel-app in Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="6c381-203">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="6c381-204">Eine app in Azure App Service bereitgestellt wird automatisch in Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="6c381-204">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="6c381-205">Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung in den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="6c381-205">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="6c381-206">Die Objekt-ID wird im Azure-Portal angezeigt, auf die **Identität** Bereich des App Service.</span><span class="sxs-lookup"><span data-stu-id="6c381-206">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="6c381-207">Geben Sie mithilfe von Azure-Befehlszeilenschnittstelle und der app Objekt-ID der app mit `list` und `get` Berechtigungen für den schlüsseltresor zugreifen:</span><span class="sxs-lookup"><span data-stu-id="6c381-207">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="6c381-208">**Die app neu starten** mithilfe von Azure-Befehlszeilenschnittstelle, PowerShell oder Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="6c381-208">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="6c381-209">Die Beispiel-app:</span><span class="sxs-lookup"><span data-stu-id="6c381-209">The sample app:</span></span>

* <span data-ttu-id="6c381-210">Erstellt eine Instanz der `AzureServiceTokenProvider` Klasse, ohne dass eine Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="6c381-210">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="6c381-211">Wenn eine Verbindungszeichenfolge nicht angegeben wird, versucht der Anbieter, um ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6c381-211">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="6c381-212">Ein neues `KeyVaultClient` wird erstellt, mit der `AzureServiceTokenProvider` Instanz-token-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="6c381-212">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="6c381-213">Die `KeyVaultClient` Instanz wird verwendet, mit einer standardmäßigen Implementierung von `IKeyVaultSecretManager` , lädt alle geheime Werte und Double-Wert-Bindestriche ersetzt (`--`) mit Doppelpunkten (`:`) im Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="6c381-213">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="6c381-214">Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen.</span><span class="sxs-lookup"><span data-stu-id="6c381-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="6c381-215">In der Entwicklungsumgebung geheime Werte haben die `_dev` suffix, weil sie von geheime Benutzerschlüssel bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="6c381-216">In der produktionsumgebung, laden Sie die Werte mit den `_prod` suffix, weil sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="6c381-217">Wenn Sie erhalten eine `Access denied` Fehler, bestätigen Sie, dass die app bei Azure AD registriert und werden, Zugriff auf den schlüsseltresor bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6c381-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="6c381-218">Vergewissern Sie sich, dass der Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="6c381-218">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="6c381-219">Verwenden Sie ein Präfix Schlüsselname</span><span class="sxs-lookup"><span data-stu-id="6c381-219">Use a key name prefix</span></span>

`AddAzureKeyVault` <span data-ttu-id="6c381-220">bietet eine Überladung, die eine Implementierung von akzeptiert `IKeyVaultSecretManager`, wodurch Sie steuern, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-220">provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="6c381-221">Beispielsweise können Sie implementieren die Schnittstelle zum Laden der geheime Werte basierend auf einem Präfixwert, die, den Sie beim Starten der app bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="6c381-221">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="6c381-222">Dadurch können Sie z. B., um Geheimnisse, die basierend auf der Version der app geladen werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-222">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="6c381-223">Verwenden Sie keine Präfixe auf Key Vault-Geheimnisse, geheime Schlüssel für mehrere apps in dem gleichen schlüsseltresor zu platzieren oder environmental Geheimnisse zu setzen (z. B. *Entwicklung* im Vergleich zu *Produktion* Geheimnisse) in der gleichen Tresor.</span><span class="sxs-lookup"><span data-stu-id="6c381-223">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="6c381-224">Es wird empfohlen, dass verschiedene apps und Entwicklungs-und produktionsumgebungen verwenden Sie separate Key Vault-Instanzen, um app-Umgebungen für das Höchstmaß an Sicherheit zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="6c381-224">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="6c381-225">Im folgenden Beispiel wird hergestellt, ein geheimen Schlüssel im Schlüssel-Tresor (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Punkte sind nicht zulässig, in Key Vault-Instanz den geheimen Namen).</span><span class="sxs-lookup"><span data-stu-id="6c381-225">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="6c381-226">Dieser geheime Schlüssel stellt ein app-Geheimnis für die app-Version 5.0.0.0 dar.</span><span class="sxs-lookup"><span data-stu-id="6c381-226">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="6c381-227">Für eine andere Version der app 5.1.0.0, ein geheimen Schlüssel auf den Schlüssel hinzugefügt wird-Tresor (und mit dem Geheimnis-Manager-Tool) für `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="6c381-227">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="6c381-228">Jede Version der app lädt seine mit versionsverwaltung durch das geheimen Wert in seine Konfiguration als `AppSecret`, Ausblasegerät aus der Version, die den geheimen Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="6c381-228">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

`AddAzureKeyVault` <span data-ttu-id="6c381-229">wird aufgerufen, mit einem benutzerdefinierten `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="6c381-229">is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="6c381-230">Die Werte für den Namen des schlüsseltresors, Anwendungs-ID und Kennwort (geheimer Clientschlüssel) werden bereitgestellt, durch die *"appSettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="6c381-230">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="6c381-231">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="6c381-231">Example values:</span></span>

* <span data-ttu-id="6c381-232">Name des schlüsseltresors:</span><span class="sxs-lookup"><span data-stu-id="6c381-232">Key vault name:</span></span> `contosovault`
* <span data-ttu-id="6c381-233">Anwendungs-ID:</span><span class="sxs-lookup"><span data-stu-id="6c381-233">Application ID:</span></span> `627e911e-43cc-61d4-992e-12db9c81b413`
* <span data-ttu-id="6c381-234">Kennwort:</span><span class="sxs-lookup"><span data-stu-id="6c381-234">Password:</span></span> `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

<span data-ttu-id="6c381-235">Die `IKeyVaultSecretManager` Implementierung reagiert wird, auf die Version-Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in der Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="6c381-235">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="6c381-236">Die `Load` Methode wird aufgerufen, indem ein anbieteralgorithmus, der durchläuft die Vault-Geheimnissen, um diejenigen zu finden, die das Version-Präfix besitzen.</span><span class="sxs-lookup"><span data-stu-id="6c381-236">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="6c381-237">Wenn ein Präfix Version wurden gefunden, die `Load`, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des Namen des geheimen Schlüssels zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="6c381-237">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="6c381-238">Es entfernt das Version-Präfix aus der Name des Geheimnisses und gibt den Rest der Namen des geheimen Schlüssels für das Laden von in der app-Konfiguration-Name-Wert-Paaren.</span><span class="sxs-lookup"><span data-stu-id="6c381-238">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="6c381-239">Bei diesem Ansatz implementiert wird:</span><span class="sxs-lookup"><span data-stu-id="6c381-239">When this approach is implemented:</span></span>

1. <span data-ttu-id="6c381-240">Die Version des in der app-Projektdatei angegeben.</span><span class="sxs-lookup"><span data-stu-id="6c381-240">The app's version specified in the app's project file.</span></span> <span data-ttu-id="6c381-241">Im folgenden Beispiel wird die Version des zum festgelegt `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="6c381-241">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="6c381-242">Überprüfen Sie, ob eine `<UserSecretsId>` Eigenschaft ist in der app Projektdatei vorhanden, in denen `{GUID}` ist eine benutzerdefinierte GUID:</span><span class="sxs-lookup"><span data-stu-id="6c381-242">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="6c381-243">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Secret Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="6c381-243">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="6c381-244">Geheime Schlüssel werden in Azure Key Vault gespeichert, mit den folgenden Azure CLI-Befehlen:</span><span class="sxs-lookup"><span data-stu-id="6c381-244">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="6c381-245">Wenn die app ausgeführt wird, werden die Key Vault-Geheimnisse geladen.</span><span class="sxs-lookup"><span data-stu-id="6c381-245">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="6c381-246">Der geheime Zeichenfolge für `5000-AppSecret` wird abgeglichen, um die Version des in der app-Projektdatei angegeben (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="6c381-246">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="6c381-247">Die Version `5000` (mit den Bindestrich), wird von den Namen des Schlüssels entfernt.</span><span class="sxs-lookup"><span data-stu-id="6c381-247">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="6c381-248">In der app, die beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` lädt das Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="6c381-248">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="6c381-249">Wenn die Version des in der Projektdatei geändert wird `5.1.0.0` und die app erneut ausgeführt wird, ist der geheime zurückgegebene Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="6c381-249">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="6c381-250">Sie bieten auch eigene `KeyVaultClient` Implementierung `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="6c381-250">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="6c381-251">Eine benutzerdefinierte Clienteinstellung ermöglicht, eine einzelne Instanz des Clients für die app freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6c381-251">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="allow-the-os-to-manage-the-x509-certificate"></a><span data-ttu-id="6c381-252">Ermöglichen Sie das Betriebssystem auf das x. 509-Zertifikat verwalten</span><span class="sxs-lookup"><span data-stu-id="6c381-252">Allow the OS to manage the X.509 certificate</span></span>

<span data-ttu-id="6c381-253">Das x. 509-Zertifikat kann vom Betriebssystem verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="6c381-253">The X.509 certificate can be managed by the OS.</span></span> <span data-ttu-id="6c381-254">Im folgenden Beispiel wird die `AddAzureKeyVault` Überladung verwenden, akzeptiert eine `X509Certificate2` aus dem Zertifikatspeicher für den Computer des aktuellen Benutzers und den Fingerabdruck eines Zertifikats durch Konfiguration angegeben:</span><span class="sxs-lookup"><span data-stu-id="6c381-254">The following example uses the `AddAzureKeyVault` overload that accepts an `X509Certificate2` from the machine's current user certificate store and a certificate thumbprint supplied by configuration:</span></span>

```csharp
// using System.Linq;
// using System.Security.Cryptography.X509Certificates;
// using Microsoft.Extensions.Configuration;

WebHost.CreateDefaultBuilder(args)
    .ConfigureAppConfiguration((context, config) =>
    {
        if (context.HostingEnvironment.IsProduction())
        {
            var builtConfig = config.Build();

            using (var store = new X509Store(StoreName.My, 
                StoreLocation.CurrentUser))
            {
                store.Open(OpenFlags.ReadOnly);
                var certs = store.Certificates
                    .Find(X509FindType.FindByThumbprint, 
                        builtConfig["CertificateThumbprint"], false);

                config.AddAzureKeyVault(
                    builtConfig["KeyVaultName"], 
                    builtConfig["AzureADApplicationId"], 
                    certs.OfType<X509Certificate2>().Single());

                store.Close();
            }
        }
    })
    .UseStartup<Startup>();
```

<span data-ttu-id="6c381-255">Weitere Informationen finden Sie unter [authentifizieren mit einem Zertifikat anstelle eines geheimen Clientschlüssels](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span><span class="sxs-lookup"><span data-stu-id="6c381-255">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="6c381-256">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="6c381-256">Bind an array to a class</span></span>

<span data-ttu-id="6c381-257">Der Anbieter ist der Konfigurationswerte in ein Array für die Bindung an ein POCO-Array lesen kann.</span><span class="sxs-lookup"><span data-stu-id="6c381-257">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="6c381-258">Wenn aus einer Konfigurationsquelle zu lesen, die Schlüssel für den Doppelpunkt enthalten kann (`:`) Trennzeichen, die einen numerischen Schlüsselsegment wird verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="6c381-258">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> `:{n}:`<span data-ttu-id="6c381-259">) auf den zugehörigen gespeicherten Wert zugreifen.</span><span class="sxs-lookup"><span data-stu-id="6c381-259">).</span></span> <span data-ttu-id="6c381-260">Weitere Informationen finden Sie unter [Konfiguration: Ein Array an eine Klasse gebunden](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="6c381-260">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="6c381-261">Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="6c381-261">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="6c381-262">In diesem Thema beschriebene Ansatz verwendet die doppelten Bindestriche (`--`) als Trennzeichen bei hierarchischen Werten (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="6c381-262">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="6c381-263">Array-Schlüssel werden in Azure Key Vault gespeichert, mit doppelten Bindestrichen und numerische wichtigsten Segmente (`--0--`, `--1--`, &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="6c381-263">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="6c381-264">Überprüfen Sie die folgenden [Serilog](https://serilog.net/) Protokollierung Anbieterkonfiguration, die von einer JSON-Datei bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6c381-264">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="6c381-265">Es gibt zwei Literale in definierte Objekt der `WriteTo` Array, das zwei Serilog entsprechend *senken*, welche Ziele für die Ausgabe der konsolenprotokollierung beschreiben:</span><span class="sxs-lookup"><span data-stu-id="6c381-265">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="6c381-266">Die Konfiguration dargestellt, in der obigen JSON-Datei befindet sich in Azure Key Vault mit doppelter Bindestrich (`--`)-Notation und numerische Segmente:</span><span class="sxs-lookup"><span data-stu-id="6c381-266">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="6c381-267">Key</span><span class="sxs-lookup"><span data-stu-id="6c381-267">Key</span></span> | <span data-ttu-id="6c381-268">Wert</span><span class="sxs-lookup"><span data-stu-id="6c381-268">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="6c381-269">Laden Sie die Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6c381-269">Reload secrets</span></span>

<span data-ttu-id="6c381-270">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="6c381-270">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="6c381-271">Abgelaufen ist, deaktiviert, und die aktualisierte Geheimnisse im schlüsseltresor werden nicht berücksichtigt, von der app bis `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6c381-271">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="6c381-272">Deaktiviert und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6c381-272">Disabled and expired secrets</span></span>

<span data-ttu-id="6c381-273">Deaktiviert und abgelaufene Geheimnisse Auslösen einer `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="6c381-273">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="6c381-274">Um zu verhindern, dass Ihre app auslösen, ersetzen Sie Ihre app, oder aktualisieren Sie das Geheimnis deaktiviert/ist abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="6c381-274">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="6c381-275">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="6c381-275">Troubleshoot</span></span>

<span data-ttu-id="6c381-276">Bei die app ein Fehler beim Laden der Konfiguration, die mithilfe des Anbieters auftritt, wird eine Fehlermeldung geschrieben, auf die [Infrastruktur von ASP.NET Core-Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6c381-276">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6c381-277">Die folgenden Bedingungen werden verhindert, dass die Konfiguration geladen:</span><span class="sxs-lookup"><span data-stu-id="6c381-277">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="6c381-278">Die app ist nicht ordnungsgemäß in Azure Active Directory konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6c381-278">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="6c381-279">Die Key Vault-Instanz ist in Azure Key Vault nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="6c381-279">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="6c381-280">Die app ist nicht autorisiert, auf den schlüsseltresor zugreifen.</span><span class="sxs-lookup"><span data-stu-id="6c381-280">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="6c381-281">Die Zugriffsrichtlinie beinhaltet keine `Get` und `List` Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="6c381-281">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="6c381-282">In den schlüsseltresor ist die Konfigurationsdaten (Name / Wert-Paar) falsch benannt, fehlen, deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="6c381-282">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="6c381-283">Die app hat den falschen schlüsseltresornamen (`KeyVaultName`), Azure AD-Anwendungs-Id (`AzureADApplicationId`), oder Azure AD-Kennwort (geheimer Clientschlüssel) (`AzureADPassword`).</span><span class="sxs-lookup"><span data-stu-id="6c381-283">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD Password (Client Secret) (`AzureADPassword`).</span></span>
* <span data-ttu-id="6c381-284">Das Azure AD-Kennwort (geheimer Clientschlüssel) (`AzureADPassword`) ist abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="6c381-284">The Azure AD Password (Client Secret) (`AzureADPassword`) is expired.</span></span>
* <span data-ttu-id="6c381-285">Der Konfigurationsschlüssel (Name) ist falsch, in der app für den Wert an, die, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="6c381-285">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c381-286">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6c381-286">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="6c381-287">Microsoft Azure: Key Vault-Instanz</span><span class="sxs-lookup"><span data-stu-id="6c381-287">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="6c381-288">Microsoft Azure: Dokumentation zu Key Vault</span><span class="sxs-lookup"><span data-stu-id="6c381-288">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="6c381-289">Das Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="6c381-289">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="6c381-290">KeyVaultClient-Klasse</span><span class="sxs-lookup"><span data-stu-id="6c381-290">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="6c381-291">Schnellstart: Festlegen und Abrufen eines geheimen Schlüssels aus Azure Key Vault mit einer .NET Web-app</span><span class="sxs-lookup"><span data-stu-id="6c381-291">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="6c381-292">Tutorial: Gewusst wie: Verwenden von Azure Key Vault mit Azure Windows Virtual Machine in .NET</span><span class="sxs-lookup"><span data-stu-id="6c381-292">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
