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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="4ccb1-103">Azure Key Vault-Konfigurationsanbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ccb1-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="4ccb1-104">Durch [Luke Latham](https://github.com/guardrex) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="4ccb1-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="4ccb1-105">In diesem Dokument wird erläutert, wie Sie mit der [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Konfigurationsanbieter zum Laden von app-Konfigurationswerte aus Azure Key Vault-Geheimnissen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="4ccb1-106">Azure Key Vault ist ein cloudbasierter Dienst, die hilft beim Schutz von kryptografischen Schlüsseln und Geheimnissen, die von apps und-Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="4ccb1-107">Häufige Szenarien für die Verwendung von Azure Key Vault mit ASP.NET Core-apps:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="4ccb1-108">Steuern des Zugriffs auf sensible Konfigurationsdaten aus.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="4ccb1-109">140-2 Level 2, die erfüllt die Anforderungen für FIPS überprüft die von Hardwaresicherheitsmodulen (HSM), wenn Konfigurationsdaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="4ccb1-110">Dieses Szenario ist für apps mit ASP.NET Core 2.1 oder höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="4ccb1-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ccb1-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="4ccb1-112">Pakete</span><span class="sxs-lookup"><span data-stu-id="4ccb1-112">Packages</span></span>

<span data-ttu-id="4ccb1-113">Um die Azure Key Vault-Konfigurationsanbieter zu verwenden, fügen Sie einen Paketverweis auf die [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) Paket.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="4ccb1-114">Übernehmen der [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) Szenario fügen Sie einen Paketverweis auf die [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) Paket.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="4ccb1-115">Zum Zeitpunkt der schreiben, der neuesten stabile Version des `Microsoft.Azure.Services.AppAuthentication`, Version `1.0.3`, bietet Unterstützung für [vom System zugewiesene verwaltete Identitäten](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="4ccb1-116">Unterstützung für *Benutzer zugewiesene Identitäten verwaltet* finden Sie in der `1.0.2-preview` Paket.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-116">Support for *user-assigned managed identities* is available in the `1.0.2-preview` package.</span></span> <span data-ttu-id="4ccb1-117">In diesem Thema veranschaulicht die Verwendung von vom System verwaltet Identitäten und die bereitgestellten Beispiel-app verwendet Version `1.0.3` von der `Microsoft.Azure.Services.AppAuthentication` Paket.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="4ccb1-118">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="4ccb1-118">Sample app</span></span>

<span data-ttu-id="4ccb1-119">Die Beispiel-app ausgeführt wird, in einem von zwei Modi, die bestimmt, indem die `#define` Anweisung am Anfang der *"Program.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="4ccb1-120">`Basic` &ndash; Veranschaulicht die Verwendung einer Azure Key Vault-Anwendungs-ID und Kennwort (geheimer Clientschlüssel), auf im schlüsseltresor gespeicherte Geheimnisse zugreifen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-120">`Basic` &ndash; Demonstrates the use of an Azure Key Vault Application ID and Password (Client Secret) to access secrets stored in the key vault.</span></span> <span data-ttu-id="4ccb1-121">Bereitstellen der `Basic` -Version des Beispiels auf einem beliebigen Host kann eine ASP.NET Core-app bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-121">Deploy the `Basic` version of the sample to any host capable of serving an ASP.NET Core app.</span></span> <span data-ttu-id="4ccb1-122">Befolgen Sie die Anweisungen in der [verwenden Anwendungs-ID und geheimer Clientschlüssel für nicht-Azure-gehostete apps](#use-application-id-and-client-secret-for-non-azure-hosted-apps) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-122">Follow the guidance in the [Use Application ID and Client Secret for non-Azure-hosted apps](#use-application-id-and-client-secret-for-non-azure-hosted-apps) section.</span></span>
* <span data-ttu-id="4ccb1-123">`Managed` &ndash; Veranschaulicht, wie [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) zum Authentifizieren von der app in Azure Key Vault mit Azure AD-Authentifizierung ohne Anmeldeinformationen, die in der app Code oder Konfiguration gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-123">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="4ccb1-124">Verwendung von verwalteten Identitäten um zu authentifizieren, sind eine Azure AD-Anwendungs-ID und Kennwort (geheimer Clientschlüssel) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-124">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="4ccb1-125">Die `Managed` -Version des Beispiels in Azure bereitgestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-125">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="4ccb1-126">Befolgen Sie die Anweisungen in der [verwenden Sie die verwaltete Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-126">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="4ccb1-127">Weitere Informationen zum Konfigurieren einer Beispiel-app mittels präprozessoranweisungen (`#define`), finden Sie unter <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-127">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="4ccb1-128">Geheimen Speicher in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="4ccb1-128">Secret storage in the Development environment</span></span>

<span data-ttu-id="4ccb1-129">Legen Sie die Geheimnisse, die lokal mithilfe der [Secret Manager-Tool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-129">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="4ccb1-130">Geheimnisse werden geladen, wenn die Beispiel-app auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, aus dem lokalen Speicher für die Geheimnis-Manager.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-130">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="4ccb1-131">Das Geheimnis-Manager-Tool erfordert eine `<UserSecretsId>` -Eigenschaft in der app-Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-131">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="4ccb1-132">Legen Sie den Wert der Eigenschaft (`{GUID}`) auf eine beliebige eindeutige GUID:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-132">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="4ccb1-133">Geheime Schlüssel werden als Name / Wert-Paaren erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-133">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="4ccb1-134">Verwenden von hierarchische Werten (Konfigurationsabschnitte) eine `:` (Doppelpunkt) als Trennzeichen in [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index) Schlüssel Namen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-134">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="4ccb1-135">Der Geheimnis-Manager wird verwendet, über eine Befehlsshell, die für das inhaltsstammverzeichnis des Projekts geöffnet, in denen `{SECRET NAME}` ist der Name und `{SECRET VALUE}` ist der Wert:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-135">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="4ccb1-136">Führen Sie die folgenden Befehle in einer Befehlsshell aus Inhalt Stamm des Projekts, um die geheimen Schlüssel für die Beispiel-app festzulegen:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-136">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="4ccb1-137">Wenn diese vertraulichen Informationen befinden sich in Azure Key Vault in der [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt der `_dev` Suffix wird geändert, um `_prod`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-137">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="4ccb1-138">Das Suffix bietet einen visuellen Hinweis in der Ausgabe der Anwendung, der angibt, der Quelle der Konfigurationswerte an.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-138">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="4ccb1-139">Geheimen Speicher in der produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ccb1-139">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="4ccb1-140">Die Anweisungen unter dem [Schnellstart: Festlegen und Abrufen ein geheimen Schlüssels aus Azure Key Vault mithilfe der Azure CLI](/azure/key-vault/quick-create-cli) Thema werden hier zusammengefasst, für das Erstellen von Azure Key Vault, und Speichern von Geheimnissen, die von der Beispiel-app verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-140">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="4ccb1-141">Das Thema für Weitere Informationen finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-141">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="4ccb1-142">Öffnen mit einer der folgenden Methoden in Azure-Cloud-Shell die [Azure-Portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="4ccb1-142">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="4ccb1-143">Wählen Sie **ausprobieren** in der oberen rechten Ecke eines Codeblocks.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-143">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="4ccb1-144">Verwenden Sie die Zeichenfolge "Azure-CLI" in das Textfeld ein.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-144">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="4ccb1-145">Öffnen Sie Cloud Shell in Ihrem Browser mit der **Cloud Shell starten** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-145">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="4ccb1-146">Wählen Sie die **Cloud Shell** im auf die Schaltfläche in der oberen rechten Ecke des Azure-Portals.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-146">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="4ccb1-147">Weitere Informationen finden Sie unter [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-147">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="4ccb1-148">Wenn Sie bereits authentifiziert sind nicht, melden Sie sich mit den `az login` Befehl.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-148">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="4ccb1-149">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, in denen `{RESOURCE GROUP NAME}` ist der Ressourcengruppenname für die neue Ressourcengruppe und `{LOCATION}` ist die Azure-Region (Rechenzentrum):</span><span class="sxs-lookup"><span data-stu-id="4ccb1-149">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="4ccb1-150">Erstellen eines schlüsseltresors in der Ressourcengruppe mit dem folgenden Befehl, in denen `{KEY VAULT NAME}` ist der Name für die neue Key Vault und `{LOCATION}` ist die Azure-Region (Rechenzentrum):</span><span class="sxs-lookup"><span data-stu-id="4ccb1-150">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="4ccb1-151">Erstellen Sie Geheimnisse im schlüsseltresor als Name / Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-151">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="4ccb1-152">Geheime Azure Key Vault-Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-152">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="4ccb1-153">Verwenden von hierarchische Werten (Konfigurationsabschnitte) `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-153">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="4ccb1-154">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt über einen Unterschlüssel in zu begrenzen [ASP.NET Core-Konfiguration](xref:fundamentals/configuration/index), sind nicht zulässig in Key Vault-Instanz den geheimen Namen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-154">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="4ccb1-155">Daher sind die beiden Striche verwendet und für einen Doppelpunkt ausgetauscht werden, wenn die geheimen Schlüssel in der app Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-155">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="4ccb1-156">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-156">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="4ccb1-157">Die Werte umfassen eine `_prod` Suffix zur Unterscheidung von den `_dev` suffix Werte in der Entwicklungsumgebung aus geheime Benutzerschlüssel.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-157">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="4ccb1-158">Ersetzen Sie dies `{KEY VAULT NAME}` durch den Namen des schlüsseltresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-158">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a><span data-ttu-id="4ccb1-159">Verwenden Sie Anwendungs-ID und geheimer Clientschlüssel für nicht-Azure gehosteten apps</span><span class="sxs-lookup"><span data-stu-id="4ccb1-159">Use Application ID and Client Secret for non-Azure-hosted apps</span></span>

<span data-ttu-id="4ccb1-160">Konfigurieren von Azure AD, Azure Key Vault, und der app, um eine Anwendungs-ID und Kennwort (geheimer Clientschlüssel) verwenden, um in einen schlüsseltresor authentifizieren **Wenn die app gehostet wird, außerhalb von Azure**.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-160">Configure Azure AD, Azure Key Vault, and the app to use an Application ID and Password (Client Secret) to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span>

> [!NOTE]
> <span data-ttu-id="4ccb1-161">Mithilfe einer Anwendungs-ID und Kennwort (geheimer Clientschlüssel) für apps, die in Azure gehostet wird zwar unterstützt, es wird empfohlen, [verwaltet Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) beim Hosten einer app in Azure.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-161">Although using an Application ID and Password (Client Secret) is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="4ccb1-162">Verwaltete Identitäten erfordert das Speichern von Anmeldeinformationen in der app oder der Konfiguration, damit es im Allgemeinen sicherer Ansatz angesehen wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-162">Managed identities require storing credentials in the app or its configuration, so it's regarded as a generally safer approach.</span></span>

<span data-ttu-id="4ccb1-163">Die Beispiel-app verwendet eine Anwendungs-ID und ein Kennwort (geheimer Clientschlüssel) Wenn die `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Basic`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-163">The sample app uses an Application ID and Password (Client Secret) when the `#define` statement at the top of the *Program.cs* file is set to `Basic`.</span></span>

1. <span data-ttu-id="4ccb1-164">Registrieren Sie einer app in Azure AD und herzustellen Sie ein Kennwort (geheimer Clientschlüssel) für die app Identität.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-164">Register the app with Azure AD and establish a Password (Client Secret) for the app's identity.</span></span>
1. <span data-ttu-id="4ccb1-165">Der Name des schlüsseltresors, Anwendungs-ID und Kennwort/geheimer Clientschlüssel der App Store *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-165">Store the key vault name, Application ID, and Password/Client Secret in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="4ccb1-166">Navigieren Sie zu **Schlüsseltresore** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-166">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="4ccb1-167">Wählen Sie die Key Vault-Instanz, die Sie in erstellt die [geheimen Speicher in der produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-167">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="4ccb1-168">Wählen Sie **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-168">Select **Access policies**.</span></span>
1. <span data-ttu-id="4ccb1-169">Wählen Sie **neu hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-169">Select **Add new**.</span></span>
1. <span data-ttu-id="4ccb1-170">Wählen Sie **Prinzipal auswählen** , und wählen Sie nach dem Namen die registrierte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-170">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="4ccb1-171">Wählen Sie die **wählen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-171">Select the **Select** button.</span></span>
1. <span data-ttu-id="4ccb1-172">Open **Berechtigungen für Geheimnis** , und geben Sie die app mit **erhalten** und **Liste** Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-172">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="4ccb1-173">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-173">Select **OK**.</span></span>
1. <span data-ttu-id="4ccb1-174">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-174">Select **Save**.</span></span>
1. <span data-ttu-id="4ccb1-175">Bereitstellen der app an.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-175">Deploy the app.</span></span>

<span data-ttu-id="4ccb1-176">Die `Basic` Beispiel-app erhält die Konfigurationswerte aus `IConfigurationRoot` mit dem gleichen Namen wie den Namen des geheimen Schlüssels:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-176">The `Basic` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="4ccb1-177">Nicht-hierarchischen Werten: Der Wert für `SecretName` wird abgerufen, mit `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-177">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="4ccb1-178">Hierarchische Werte (Abschnitte): Verwendung `:` (Doppelpunkt)-Notation oder der `GetSection` -Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-178">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="4ccb1-179">Verwenden Sie einen dieser Ansätze, um den Konfigurationswert zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-179">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="4ccb1-180">Die app ruft `AddAzureKeyVault` mit Werten, die vom der *"appSettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-180">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

<span data-ttu-id="4ccb1-181">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-181">Example values:</span></span>

* <span data-ttu-id="4ccb1-182">Name des schlüsseltresors: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-182">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="4ccb1-183">Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-183">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="4ccb1-184">Kennwort: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-184">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="4ccb1-185">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-185">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="4ccb1-186">Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-186">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="4ccb1-187">In der Entwicklungsumgebung, laden Sie geheime Werte mit den `_dev` Suffix.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-187">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="4ccb1-188">In der produktionsumgebung, laden Sie die Werte mit den `_prod` Suffix.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-188">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="4ccb1-189">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4ccb1-189">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="4ccb1-190">**Eine app in Azure bereitgestellten** profitieren von [verwaltet Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview), die die app für die Authentifizierung mit Azure Key Vault ermöglicht die Verwendung von Azure AD-Authentifizierung ohne Anmeldeinformationen (Anwendungs-ID und Password/Client Geheimnis) in der app gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-190">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="4ccb1-191">Die Beispiel-app verwaltete Identitäten verwendet, für die Azure-Ressourcen bei der `#define` Anweisung am Anfang der *"Program.cs"* Datei nastaven NA hodnotu `Managed`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-191">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="4ccb1-192">Geben Sie den Namen des schlüsseltresors in der app *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-192">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="4ccb1-193">Die Beispiel-app nicht erforderlich, eine Anwendungs-ID und Kennwort (geheimer Clientschlüssel) bei Festlegung auf den `Managed` Version, sodass Sie die Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-193">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="4ccb1-194">Die app in Azure bereitgestellt wird, und Azure authentifiziert die app für den Zugriff auf Azure Key Vault verwenden nur den Namen des schlüsseltresors in gespeicherten die *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-194">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="4ccb1-195">Stellen Sie die Beispiel-app in Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-195">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="4ccb1-196">Eine app in Azure App Service bereitgestellt wird automatisch in Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-196">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="4ccb1-197">Rufen Sie die Objekt-ID aus der Bereitstellung für die Verwendung in den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-197">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="4ccb1-198">Die Objekt-ID wird im Azure-Portal angezeigt, auf die **Identität** Bereich des App Service.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-198">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="4ccb1-199">Geben Sie mithilfe von Azure-Befehlszeilenschnittstelle und der app Objekt-ID der app mit `list` und `get` Berechtigungen für den schlüsseltresor zugreifen:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-199">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="4ccb1-200">**Die app neu starten** mithilfe von Azure-Befehlszeilenschnittstelle, PowerShell oder Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-200">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="4ccb1-201">Die Beispiel-app:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-201">The sample app:</span></span>

* <span data-ttu-id="4ccb1-202">Erstellt eine Instanz der `AzureServiceTokenProvider` Klasse, ohne dass eine Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-202">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="4ccb1-203">Wenn eine Verbindungszeichenfolge nicht angegeben wird, versucht der Anbieter, um ein Zugriffstoken von verwalteten Identitäten für Azure-Ressourcen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-203">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="4ccb1-204">Ein neues `KeyVaultClient` wird erstellt, mit der `AzureServiceTokenProvider` Instanz-token-Rückruf.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-204">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="4ccb1-205">Die `KeyVaultClient` Instanz wird verwendet, mit einer standardmäßigen Implementierung von `IKeyVaultSecretManager` , lädt alle geheime Werte und Double-Wert-Bindestriche ersetzt (`--`) mit Doppelpunkten (`:`) im Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-205">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="4ccb1-206">Wenn Sie die app ausführen, zeigt eine Webseite geheime Werte geladen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-206">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="4ccb1-207">In der Entwicklungsumgebung geheime Werte haben die `_dev` suffix, weil sie von geheime Benutzerschlüssel bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-207">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="4ccb1-208">In der produktionsumgebung, laden Sie die Werte mit den `_prod` suffix, weil sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-208">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="4ccb1-209">Wenn Sie erhalten eine `Access denied` Fehler, bestätigen Sie, dass die app bei Azure AD registriert und werden, Zugriff auf den schlüsseltresor bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-209">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="4ccb1-210">Vergewissern Sie sich, dass der Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-210">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="4ccb1-211">Verwenden Sie ein Präfix Schlüsselname</span><span class="sxs-lookup"><span data-stu-id="4ccb1-211">Use a key name prefix</span></span>

<span data-ttu-id="4ccb1-212">`AddAzureKeyVault` bietet eine Überladung, die eine Implementierung von akzeptiert `IKeyVaultSecretManager`, wodurch Sie steuern, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-212">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="4ccb1-213">Beispielsweise können Sie implementieren die Schnittstelle zum Laden der geheime Werte basierend auf einem Präfixwert, die, den Sie beim Starten der app bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-213">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="4ccb1-214">Dadurch können Sie z. B., um Geheimnisse, die basierend auf der Version der app geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-214">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="4ccb1-215">Verwenden Sie keine Präfixe auf Key Vault-Geheimnisse, geheime Schlüssel für mehrere apps in dem gleichen schlüsseltresor zu platzieren oder environmental Geheimnisse zu setzen (z. B. *Entwicklung* im Vergleich zu *Produktion* Geheimnisse) in der gleichen Tresor.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-215">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="4ccb1-216">Es wird empfohlen, dass verschiedene apps und Entwicklungs-und produktionsumgebungen verwenden Sie separate Key Vault-Instanzen, um app-Umgebungen für das Höchstmaß an Sicherheit zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-216">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="4ccb1-217">Im folgenden Beispiel wird hergestellt, ein geheimen Schlüssel im Schlüssel-Tresor (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Punkte sind nicht zulässig, in Key Vault-Instanz den geheimen Namen).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-217">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="4ccb1-218">Dieser geheime Schlüssel stellt ein app-Geheimnis für die app-Version 5.0.0.0 dar.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-218">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="4ccb1-219">Für eine andere Version der app 5.1.0.0, ein geheimen Schlüssel auf den Schlüssel hinzugefügt wird-Tresor (und mit dem Geheimnis-Manager-Tool) für `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-219">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="4ccb1-220">Jede Version der app lädt seine mit versionsverwaltung durch das geheimen Wert in seine Konfiguration als `AppSecret`, Ausblasegerät aus der Version, die den geheimen Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-220">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="4ccb1-221">`AddAzureKeyVault` wird aufgerufen, mit einem benutzerdefinierten `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-221">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="4ccb1-222">Die Werte für den Namen des schlüsseltresors, Anwendungs-ID und Kennwort (geheimer Clientschlüssel) werden bereitgestellt, durch die *"appSettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-222">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="4ccb1-223">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-223">Example values:</span></span>

* <span data-ttu-id="4ccb1-224">Name des schlüsseltresors: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-224">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="4ccb1-225">Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-225">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="4ccb1-226">Kennwort: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="4ccb1-226">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="4ccb1-227">Die `IKeyVaultSecretManager` Implementierung reagiert wird, auf die Version-Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in der Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-227">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="4ccb1-228">Die `Load` Methode wird aufgerufen, indem ein anbieteralgorithmus, der durchläuft die Vault-Geheimnissen, um diejenigen zu finden, die das Version-Präfix besitzen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-228">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="4ccb1-229">Wenn ein Präfix Version wurden gefunden, die `Load`, verwendet der Algorithmus die `GetKey` Methode, um den Konfigurationsnamen des Namen des geheimen Schlüssels zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-229">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="4ccb1-230">Es entfernt das Version-Präfix aus der Name des Geheimnisses und gibt den Rest der Namen des geheimen Schlüssels für das Laden von in der app-Konfiguration-Name-Wert-Paaren.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-230">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="4ccb1-231">Bei diesem Ansatz implementiert wird:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-231">When this approach is implemented:</span></span>

1. <span data-ttu-id="4ccb1-232">Die Version des in der app-Projektdatei angegeben.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-232">The app's version specified in the app's project file.</span></span> <span data-ttu-id="4ccb1-233">Im folgenden Beispiel wird die Version des zum festgelegt `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-233">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="4ccb1-234">Überprüfen Sie, ob eine `<UserSecretsId>` Eigenschaft ist in der app Projektdatei vorhanden, in denen `{GUID}` ist eine benutzerdefinierte GUID:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-234">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="4ccb1-235">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Secret Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="4ccb1-235">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="4ccb1-236">Geheime Schlüssel werden in Azure Key Vault gespeichert, mit den folgenden Azure CLI-Befehlen:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-236">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="4ccb1-237">Wenn die app ausgeführt wird, werden die Key Vault-Geheimnisse geladen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-237">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="4ccb1-238">Der geheime Zeichenfolge für `5000-AppSecret` wird abgeglichen, um die Version des in der app-Projektdatei angegeben (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-238">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="4ccb1-239">Die Version `5000` (mit den Bindestrich), wird von den Namen des Schlüssels entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-239">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="4ccb1-240">In der app, die beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` lädt das Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-240">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="4ccb1-241">Wenn die Version des in der Projektdatei geändert wird `5.1.0.0` und die app erneut ausgeführt wird, ist der geheime zurückgegebene Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und `5.1.0.0_secret_value_prod` in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-241">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="4ccb1-242">Sie bieten auch eigene `KeyVaultClient` Implementierung `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-242">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="4ccb1-243">Eine benutzerdefinierte Clienteinstellung ermöglicht, eine einzelne Instanz des Clients für die app freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-243">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a><span data-ttu-id="4ccb1-244">Authentifizieren Sie mit Azure Key Vault mit einem x. 509-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="4ccb1-244">Authenticate to Azure Key Vault with an X.509 certificate</span></span>

<span data-ttu-id="4ccb1-245">Wenn Sie eine .NET Framework-app in einer Umgebung entwickeln, die Zertifikate unterstützt, können Sie mit einem x. 509-Zertifikat in Azure Key Vault authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-245">When developing a .NET Framework app in an environment that supports certificates, you can authenticate to Azure Key Vault with an X.509 certificate.</span></span> <span data-ttu-id="4ccb1-246">Privaten Schlüssel des x. 509-Zertifikats wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-246">The X.509 certificate's private key is managed by the OS.</span></span> <span data-ttu-id="4ccb1-247">Weitere Informationen finden Sie unter [authentifizieren mit einem Zertifikat anstelle eines geheimen Clientschlüssels](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-247">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span> <span data-ttu-id="4ccb1-248">Verwenden der `AddAzureKeyVault` Überladung verwenden, akzeptiert eine `X509Certificate2` (`_env` im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-248">Use the `AddAzureKeyVault` overload that accepts an `X509Certificate2` (`_env` in the following example :</span></span>

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

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="4ccb1-249">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="4ccb1-249">Bind an array to a class</span></span>

<span data-ttu-id="4ccb1-250">Der Anbieter ist der Konfigurationswerte in ein Array für die Bindung an ein POCO-Array lesen kann.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-250">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="4ccb1-251">Wenn aus einer Konfigurationsquelle zu lesen, die Schlüssel für den Doppelpunkt enthalten kann (`:`) Trennzeichen, die einen numerischen Schlüsselsegment wird verwendet, um die Schlüssel zu unterscheiden, aus denen ein Array (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="4ccb1-251">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="4ccb1-252">`:{n}:`) angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-252">`:{n}:`).</span></span> <span data-ttu-id="4ccb1-253">Weitere Informationen finden Sie unter [Konfiguration: Ein Array an eine Klasse gebunden](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-253">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="4ccb1-254">Azure Key Vault-Schlüssel können keinen Doppelpunkt als Trennzeichen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-254">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="4ccb1-255">In diesem Thema beschriebene Ansatz verwendet die doppelten Bindestriche (`--`) als Trennzeichen bei hierarchischen Werten (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-255">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="4ccb1-256">Array-Schlüssel werden in Azure Key Vault gespeichert, mit doppelten Bindestrichen und numerische wichtigsten Segmente (`--0--`, `--1--`,...</span><span class="sxs-lookup"><span data-stu-id="4ccb1-256">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, …</span></span> <span data-ttu-id="4ccb1-257">`--{n}--`) angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-257">`--{n}--`).</span></span>

<span data-ttu-id="4ccb1-258">Überprüfen Sie die folgenden [Serilog](https://serilog.net/) Protokollierung Anbieterkonfiguration, die von einer JSON-Datei bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-258">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="4ccb1-259">Es gibt zwei Literale in definierte Objekt der `WriteTo` Array, das zwei Serilog entsprechend *senken*, welche Ziele für die Ausgabe der konsolenprotokollierung beschreiben:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-259">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="4ccb1-260">Die Konfiguration dargestellt, in der obigen JSON-Datei befindet sich in Azure Key Vault mit doppelter Bindestrich (`--`)-Notation und numerische Segmente:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-260">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="4ccb1-261">Key</span><span class="sxs-lookup"><span data-stu-id="4ccb1-261">Key</span></span> | <span data-ttu-id="4ccb1-262">Wert</span><span class="sxs-lookup"><span data-stu-id="4ccb1-262">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="4ccb1-263">Laden Sie die Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="4ccb1-263">Reload secrets</span></span>

<span data-ttu-id="4ccb1-264">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-264">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="4ccb1-265">Abgelaufen ist, deaktiviert, und die aktualisierte Geheimnisse im schlüsseltresor werden nicht berücksichtigt, von der app bis `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-265">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="4ccb1-266">Deaktiviert und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="4ccb1-266">Disabled and expired secrets</span></span>

<span data-ttu-id="4ccb1-267">Deaktiviert und abgelaufene Geheimnisse Auslösen einer `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-267">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="4ccb1-268">Um zu verhindern, dass Ihre app auslösen, ersetzen Sie Ihre app, oder aktualisieren Sie das Geheimnis deaktiviert/ist abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-268">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="4ccb1-269">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="4ccb1-269">Troubleshoot</span></span>

<span data-ttu-id="4ccb1-270">Bei die app ein Fehler beim Laden der Konfiguration, die mithilfe des Anbieters auftritt, wird eine Fehlermeldung geschrieben, auf die [Infrastruktur von ASP.NET Core-Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-270">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="4ccb1-271">Die folgenden Bedingungen werden verhindert, dass die Konfiguration geladen:</span><span class="sxs-lookup"><span data-stu-id="4ccb1-271">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="4ccb1-272">Die app ist nicht ordnungsgemäß in Azure Active Directory konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-272">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="4ccb1-273">Die Key Vault-Instanz ist in Azure Key Vault nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-273">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="4ccb1-274">Die app ist nicht autorisiert, auf den schlüsseltresor zugreifen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-274">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="4ccb1-275">Die Zugriffsrichtlinie beinhaltet keine `Get` und `List` Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-275">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="4ccb1-276">In den schlüsseltresor ist die Konfigurationsdaten (Name / Wert-Paar) falsch benannt, fehlen, deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-276">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="4ccb1-277">Die app hat den falschen schlüsseltresornamen (`Vault`), Azure AD-App-Id (`ClientId`), oder Azure AD Key (`ClientSecret`).</span><span class="sxs-lookup"><span data-stu-id="4ccb1-277">The app has the wrong key vault name (`Vault`), Azure AD App Id (`ClientId`), or Azure AD Key (`ClientSecret`).</span></span>
* <span data-ttu-id="4ccb1-278">Die Azure AD Key (`ClientSecret`) ist abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-278">The Azure AD Key (`ClientSecret`) is expired.</span></span>
* <span data-ttu-id="4ccb1-279">Der Konfigurationsschlüssel (Name) ist falsch, in der app für den Wert an, die, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="4ccb1-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ccb1-280">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4ccb1-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="4ccb1-281">Microsoft Azure: Key Vault-Instanz</span><span class="sxs-lookup"><span data-stu-id="4ccb1-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="4ccb1-282">Microsoft Azure: Dokumentation zu Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ccb1-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="4ccb1-283">Das Generieren und Übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ccb1-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="4ccb1-284">KeyVaultClient-Klasse</span><span class="sxs-lookup"><span data-stu-id="4ccb1-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
