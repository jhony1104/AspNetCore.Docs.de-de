---
<span data-ttu-id="783d8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-102">'Blazor'</span></span>
- <span data-ttu-id="783d8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-103">'Identity'</span></span>
- <span data-ttu-id="783d8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-105">'Razor'</span></span>
- <span data-ttu-id="783d8-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-106">'SignalR' uid:</span></span> 

---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="783d8-107">Azure Key Vault Konfigurations Anbieters in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="783d8-107">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="783d8-108">Von [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="783d8-108">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="783d8-109">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="783d8-109">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="783d8-110">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-110">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="783d8-111">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="783d8-111">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="783d8-112">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="783d8-112">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="783d8-113">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="783d8-113">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="783d8-114">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="783d8-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="783d8-115">Pakete</span><span class="sxs-lookup"><span data-stu-id="783d8-115">Packages</span></span>

<span data-ttu-id="783d8-116">Fügen Sie dem Paket " [Microsoft. Extensions. Configuration. azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="783d8-116">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="783d8-117">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="783d8-117">Sample app</span></span>

<span data-ttu-id="783d8-118">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="783d8-118">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="783d8-119">`Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="783d8-119">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="783d8-120">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="783d8-120">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="783d8-121">`Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="783d8-121">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="783d8-122">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="783d8-122">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="783d8-123">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-123">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="783d8-124">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="783d8-124">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="783d8-125">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="783d8-125">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="783d8-126">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="783d8-126">Secret storage in the Development environment</span></span>

<span data-ttu-id="783d8-127">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="783d8-127">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="783d8-128">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Speicher des geheimen Haupt Schlüssels geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-128">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="783d8-129">Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-129">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="783d8-130">Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="783d8-130">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="783d8-131">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="783d8-131">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="783d8-132">Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="783d8-132">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="783d8-133">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-133">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="783d8-134">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="783d8-134">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="783d8-135">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` .</span><span class="sxs-lookup"><span data-stu-id="783d8-135">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="783d8-136">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-136">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="783d8-137">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-137">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="783d8-138">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-138">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="783d8-139">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="783d8-139">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="783d8-140">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="783d8-140">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="783d8-141">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="783d8-141">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="783d8-142">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="783d8-142">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="783d8-143">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="783d8-143">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="783d8-144">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-144">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="783d8-145">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="783d8-145">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="783d8-146">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="783d8-146">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="783d8-147">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-147">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="783d8-148">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-148">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="783d8-149">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="783d8-149">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="783d8-150">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="783d8-150">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="783d8-151">Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="783d8-151">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="783d8-152">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="783d8-152">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="783d8-153">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-153">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="783d8-154">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="783d8-154">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="783d8-155">Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-155">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="783d8-156">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="783d8-156">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="783d8-157">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="783d8-157">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="783d8-158">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="783d8-158">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="783d8-159">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="783d8-159">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="783d8-160">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-160">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="783d8-161">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="783d8-161">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="783d8-162">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="783d8-162">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="783d8-163">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="783d8-163">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="783d8-164">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="783d8-164">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="783d8-165">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="783d8-165">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="783d8-166">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="783d8-166">Marking the key as exportable is optional.</span></span> <span data-ttu-id="783d8-167">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-167">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="783d8-168">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="783d8-168">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="783d8-169">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="783d8-169">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="783d8-170">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="783d8-170">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="783d8-171">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-171">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="783d8-172">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="783d8-172">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="783d8-173">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="783d8-173">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="783d8-174">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="783d8-174">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="783d8-175">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der *appSettings. JSON* -Datei der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-175">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="783d8-176">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="783d8-176">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="783d8-177">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="783d8-177">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="783d8-178">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="783d8-178">Select **Access policies**.</span></span>
1. <span data-ttu-id="783d8-179">Wählen Sie **Zugriffs Richtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-179">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="783d8-180">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-180">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="783d8-181">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="783d8-182">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="783d8-183">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="783d8-183">Select **OK**.</span></span>
1. <span data-ttu-id="783d8-184">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-184">Select **Save**.</span></span>
1. <span data-ttu-id="783d8-185">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-185">Deploy the app.</span></span>

<span data-ttu-id="783d8-186">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="783d8-186">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="783d8-187">Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="783d8-187">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="783d8-188">Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="783d8-188">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="783d8-189">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="783d8-189">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="783d8-190">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="783d8-190">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="783d8-191">Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der *appSettings. JSON* -Datei bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="783d8-191">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="783d8-192">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="783d8-192">Example values:</span></span>

* <span data-ttu-id="783d8-193">Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="783d8-193">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="783d8-194">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="783d8-194">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="783d8-195">Zertifikat Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="783d8-195">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="783d8-196">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="783d8-196">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="783d8-197">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-197">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="783d8-198">In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-198">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="783d8-199">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-199">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="783d8-200">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="783d8-200">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="783d8-201">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="783d8-201">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="783d8-202">In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .</span><span class="sxs-lookup"><span data-stu-id="783d8-202">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="783d8-203">Geben Sie den Tresor Namen in die Datei " *appSettings. JSON* " der APP ein.</span><span class="sxs-lookup"><span data-stu-id="783d8-203">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="783d8-204">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="783d8-204">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="783d8-205">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei " *appSettings. JSON* " gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="783d8-205">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="783d8-206">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-206">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="783d8-207">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-207">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="783d8-208">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-208">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="783d8-209">Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-209">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="783d8-210">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="783d8-210">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="783d8-211">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="783d8-211">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="783d8-212">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="783d8-212">The sample app:</span></span>

* <span data-ttu-id="783d8-213">Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="783d8-213">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="783d8-214">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="783d8-214">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="783d8-215">Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="783d8-215">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="783d8-216">Die- <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer Standard Implementierung von verwendet <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="783d8-216">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="783d8-217">Beispiel Wert für Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="783d8-217">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="783d8-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="783d8-218">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="783d8-219">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-219">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="783d8-220">In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-220">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="783d8-221">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-221">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="783d8-222">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="783d8-222">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="783d8-223">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="783d8-223">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="783d8-224">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="783d8-224">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="783d8-225">Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="783d8-225">Configuration options</span></span>

<span data-ttu-id="783d8-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>kann akzeptieren <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="783d8-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="783d8-227">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="783d8-227">Property</span></span>         | <span data-ttu-id="783d8-228">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="783d8-228">Description</span></span> |
| ---
<span data-ttu-id="783d8-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-230">'Blazor'</span></span>
- <span data-ttu-id="783d8-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-231">'Identity'</span></span>
- <span data-ttu-id="783d8-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-233">'Razor'</span></span>
- <span data-ttu-id="783d8-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-236">'Blazor'</span></span>
- <span data-ttu-id="783d8-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-237">'Identity'</span></span>
- <span data-ttu-id="783d8-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-239">'Razor'</span></span>
- <span data-ttu-id="783d8-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-242">'Blazor'</span></span>
- <span data-ttu-id="783d8-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-243">'Identity'</span></span>
- <span data-ttu-id="783d8-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-245">'Razor'</span></span>
- <span data-ttu-id="783d8-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-248">'Blazor'</span></span>
- <span data-ttu-id="783d8-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-249">'Identity'</span></span>
- <span data-ttu-id="783d8-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-251">'Razor'</span></span>
- <span data-ttu-id="783d8-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-254">'Blazor'</span></span>
- <span data-ttu-id="783d8-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-255">'Identity'</span></span>
- <span data-ttu-id="783d8-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-257">'Razor'</span></span>
- <span data-ttu-id="783d8-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-260">'Blazor'</span></span>
- <span data-ttu-id="783d8-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-261">'Identity'</span></span>
- <span data-ttu-id="783d8-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-263">'Razor'</span></span>
- <span data-ttu-id="783d8-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-264">'SignalR' uid:</span></span> 

<span data-ttu-id="783d8-265">-------- | ---Titel: Author: Description: monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="783d8-265">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-266">'Blazor'</span></span>
- <span data-ttu-id="783d8-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-267">'Identity'</span></span>
- <span data-ttu-id="783d8-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-269">'Razor'</span></span>
- <span data-ttu-id="783d8-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-272">'Blazor'</span></span>
- <span data-ttu-id="783d8-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-273">'Identity'</span></span>
- <span data-ttu-id="783d8-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-275">'Razor'</span></span>
- <span data-ttu-id="783d8-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="783d8-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="783d8-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="783d8-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="783d8-278">'Blazor'</span></span>
- <span data-ttu-id="783d8-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="783d8-279">'Identity'</span></span>
- <span data-ttu-id="783d8-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="783d8-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="783d8-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="783d8-281">'Razor'</span></span>
- <span data-ttu-id="783d8-282">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="783d8-282">'SignalR' uid:</span></span> 

<span data-ttu-id="783d8-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> , der zum Abrufen von Werten verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="783d8-283">------ | | `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> <span data-ttu-id="783d8-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> zum Steuern des geheimen Schlüssels verwendete Instanz.</span><span class="sxs-lookup"><span data-stu-id="783d8-284">| | `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> <span data-ttu-id="783d8-285">| | `ReloadInterval` | `Timespan` , um zwischen versuchen zu warten, den Schlüssel Tresor auf Änderungen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="783d8-285">| | `ReloadInterval` | `Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="783d8-286">Der Standardwert ist `null` (die Konfiguration wird nicht neu geladen).</span><span class="sxs-lookup"><span data-stu-id="783d8-286">The default value is `null` (configuration isn't reloaded).</span></span> <span data-ttu-id="783d8-287">| | `Vault`          | Key Vault-URI.</span><span class="sxs-lookup"><span data-stu-id="783d8-287">| | `Vault`          | Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="783d8-288">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="783d8-288">Use a key name prefix</span></span>

<span data-ttu-id="783d8-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>stellt eine Überladung bereit, die eine Implementierung von akzeptiert <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-289"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="783d8-290">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="783d8-290">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="783d8-291">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="783d8-291">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="783d8-292">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="783d8-292">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="783d8-293">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="783d8-293">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="783d8-294">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="783d8-294">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="783d8-295">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="783d8-295">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="783d8-296">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="783d8-296">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="783d8-297">Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-297">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="783d8-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem benutzerdefinierten aufgerufen <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="783d8-298"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="783d8-299">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="783d8-299">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="783d8-300">`Load`lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="783d8-300">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="783d8-301">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-301">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="783d8-302">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="783d8-302">`GetKey`:</span></span>
  * <span data-ttu-id="783d8-303">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="783d8-303">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="783d8-304">Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="783d8-304">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="783d8-305">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="783d8-305">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="783d8-306">Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="783d8-306">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="783d8-307">Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="783d8-307">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="783d8-308">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="783d8-308">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="783d8-309">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-309">When this approach is implemented:</span></span>

1. <span data-ttu-id="783d8-310">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-310">The app's version specified in the app's project file.</span></span> <span data-ttu-id="783d8-311">Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="783d8-311">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="783d8-312">Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-312">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="783d8-313">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="783d8-313">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="783d8-314">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="783d8-314">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="783d8-315">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-315">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="783d8-316">Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="783d8-316">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="783d8-317">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="783d8-317">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="783d8-318">Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-318">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="783d8-319">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .</span><span class="sxs-lookup"><span data-stu-id="783d8-319">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="783d8-320">Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für bereitstellen <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="783d8-320">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="783d8-321">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="783d8-321">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="783d8-322">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="783d8-322">Bind an array to a class</span></span>

<span data-ttu-id="783d8-323">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="783d8-323">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="783d8-324">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="783d8-324">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="783d8-325">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="783d8-325">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="783d8-326">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-326">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="783d8-327">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="783d8-327">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="783d8-328">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="783d8-328">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="783d8-329">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="783d8-329">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="783d8-330">Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken*reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="783d8-330">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="783d8-331">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="783d8-331">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="783d8-332">Key</span><span class="sxs-lookup"><span data-stu-id="783d8-332">Key</span></span> | <span data-ttu-id="783d8-333">Wert</span><span class="sxs-lookup"><span data-stu-id="783d8-333">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="783d8-334">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="783d8-334">Reload secrets</span></span>

<span data-ttu-id="783d8-335">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-335">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="783d8-336">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-336">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="783d8-337">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="783d8-337">Disabled and expired secrets</span></span>

<span data-ttu-id="783d8-338">Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="783d8-338">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="783d8-339">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="783d8-339">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="783d8-340">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="783d8-340">Troubleshoot</span></span>

<span data-ttu-id="783d8-341">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="783d8-341">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="783d8-342">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="783d8-342">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="783d8-343">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="783d8-343">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="783d8-344">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="783d8-344">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="783d8-345">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="783d8-345">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="783d8-346">Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="783d8-346">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="783d8-347">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="783d8-347">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="783d8-348">Die APP hat den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="783d8-348">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="783d8-349">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="783d8-349">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="783d8-350">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="783d8-350">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="783d8-351">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="783d8-351">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="783d8-352">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-352">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="783d8-353">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="783d8-353">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="783d8-354">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-354">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="783d8-355">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="783d8-355">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="783d8-356">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="783d8-356">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="783d8-357">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="783d8-357">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="783d8-358">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="783d8-358">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="783d8-359">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-359">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="783d8-360">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="783d8-360">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="783d8-361">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="783d8-361">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="783d8-362">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="783d8-362">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="783d8-363">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="783d8-363">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="783d8-364">Pakete</span><span class="sxs-lookup"><span data-stu-id="783d8-364">Packages</span></span>

<span data-ttu-id="783d8-365">Fügen Sie dem Paket " [Microsoft. Extensions. Configuration. azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) " einen Paket Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="783d8-365">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="783d8-366">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="783d8-366">Sample app</span></span>

<span data-ttu-id="783d8-367">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="783d8-367">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="783d8-368">`Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="783d8-368">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="783d8-369">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="783d8-369">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="783d8-370">`Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="783d8-370">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="783d8-371">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="783d8-371">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="783d8-372">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-372">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="783d8-373">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="783d8-373">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="783d8-374">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="783d8-374">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="783d8-375">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="783d8-375">Secret storage in the Development environment</span></span>

<span data-ttu-id="783d8-376">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="783d8-376">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="783d8-377">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden Geheimnisse aus dem lokalen Speicher des geheimen Haupt Schlüssels geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-377">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="783d8-378">Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-378">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="783d8-379">Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="783d8-379">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="783d8-380">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="783d8-380">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="783d8-381">Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="783d8-381">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="783d8-382">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-382">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="783d8-383">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="783d8-383">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="783d8-384">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` .</span><span class="sxs-lookup"><span data-stu-id="783d8-384">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="783d8-385">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-385">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="783d8-386">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-386">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="783d8-387">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-387">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="783d8-388">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="783d8-388">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="783d8-389">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="783d8-389">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="783d8-390">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="783d8-390">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="783d8-391">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="783d8-391">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="783d8-392">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="783d8-392">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="783d8-393">Wählen Sie im Azure-Portal rechts oben im Menü die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-393">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="783d8-394">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="783d8-394">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="783d8-395">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="783d8-395">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="783d8-396">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-396">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="783d8-397">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-397">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="783d8-398">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="783d8-398">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="783d8-399">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="783d8-399">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="783d8-400">Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="783d8-400">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="783d8-401">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="783d8-401">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="783d8-402">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-402">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="783d8-403">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="783d8-403">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="783d8-404">Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-404">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="783d8-405">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="783d8-405">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="783d8-406">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="783d8-406">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="783d8-407">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="783d8-407">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="783d8-408">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="783d8-408">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="783d8-409">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-409">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="783d8-410">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="783d8-410">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="783d8-411">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="783d8-411">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="783d8-412">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="783d8-412">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="783d8-413">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="783d8-413">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="783d8-414">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="783d8-414">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="783d8-415">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="783d8-415">Marking the key as exportable is optional.</span></span> <span data-ttu-id="783d8-416">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-416">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="783d8-417">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="783d8-417">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="783d8-418">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="783d8-418">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="783d8-419">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="783d8-419">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="783d8-420">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-420">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="783d8-421">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="783d8-421">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="783d8-422">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="783d8-422">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="783d8-423">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="783d8-423">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="783d8-424">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der *appSettings. JSON* -Datei der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-424">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="783d8-425">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="783d8-425">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="783d8-426">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="783d8-426">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="783d8-427">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="783d8-427">Select **Access policies**.</span></span>
1. <span data-ttu-id="783d8-428">Wählen Sie **Zugriffs Richtlinie hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-428">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="783d8-429">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-429">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="783d8-430">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-430">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="783d8-431">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-431">Select the **Select** button.</span></span>
1. <span data-ttu-id="783d8-432">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="783d8-432">Select **OK**.</span></span>
1. <span data-ttu-id="783d8-433">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="783d8-433">Select **Save**.</span></span>
1. <span data-ttu-id="783d8-434">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-434">Deploy the app.</span></span>

<span data-ttu-id="783d8-435">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="783d8-435">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="783d8-436">Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="783d8-436">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="783d8-437">Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="783d8-437">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="783d8-438">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="783d8-438">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="783d8-439">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="783d8-439">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="783d8-440">Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der *appSettings. JSON* -Datei bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="783d8-440">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="783d8-441">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="783d8-441">Example values:</span></span>

* <span data-ttu-id="783d8-442">Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="783d8-442">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="783d8-443">Anwendungs-ID:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="783d8-443">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="783d8-444">Zertifikat Fingerabdruck:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="783d8-444">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="783d8-445">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="783d8-445">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="783d8-446">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-446">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="783d8-447">In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-447">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="783d8-448">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-448">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="783d8-449">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="783d8-449">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="783d8-450">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="783d8-450">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="783d8-451">In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .</span><span class="sxs-lookup"><span data-stu-id="783d8-451">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="783d8-452">Geben Sie den Tresor Namen in die Datei " *appSettings. JSON* " der APP ein.</span><span class="sxs-lookup"><span data-stu-id="783d8-452">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="783d8-453">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="783d8-453">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="783d8-454">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei " *appSettings. JSON* " gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="783d8-454">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="783d8-455">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="783d8-455">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="783d8-456">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-456">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="783d8-457">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-457">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="783d8-458">Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-458">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="783d8-459">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="783d8-459">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="783d8-460">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="783d8-460">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="783d8-461">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="783d8-461">The sample app:</span></span>

* <span data-ttu-id="783d8-462">Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="783d8-462">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="783d8-463">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="783d8-463">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="783d8-464">Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="783d8-464">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="783d8-465">Die- <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer Standard Implementierung von verwendet <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="783d8-465">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="783d8-466">Beispiel Wert für Key Vault-Name:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="783d8-466">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="783d8-467">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="783d8-467">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="783d8-468">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="783d8-468">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="783d8-469">In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-469">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="783d8-470">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-470">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="783d8-471">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="783d8-471">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="783d8-472">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="783d8-472">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="783d8-473">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="783d8-473">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="783d8-474">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="783d8-474">Use a key name prefix</span></span>

<span data-ttu-id="783d8-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>stellt eine Überladung bereit, die eine Implementierung von akzeptiert <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="783d8-475"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="783d8-476">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="783d8-476">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="783d8-477">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="783d8-477">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="783d8-478">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="783d8-478">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="783d8-479">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="783d8-479">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="783d8-480">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="783d8-480">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="783d8-481">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="783d8-481">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="783d8-482">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="783d8-482">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="783d8-483">Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-483">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="783d8-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>wird mit einem benutzerdefinierten aufgerufen <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="783d8-484"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="783d8-485">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="783d8-485">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="783d8-486">`Load`lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="783d8-486">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="783d8-487">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-487">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="783d8-488">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="783d8-488">`GetKey`:</span></span>
  * <span data-ttu-id="783d8-489">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="783d8-489">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="783d8-490">Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="783d8-490">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="783d8-491">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="783d8-491">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="783d8-492">Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="783d8-492">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="783d8-493">Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="783d8-493">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="783d8-494">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="783d8-494">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="783d8-495">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-495">When this approach is implemented:</span></span>

1. <span data-ttu-id="783d8-496">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="783d8-496">The app's version specified in the app's project file.</span></span> <span data-ttu-id="783d8-497">Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="783d8-497">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="783d8-498">Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="783d8-498">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="783d8-499">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="783d8-499">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="783d8-500">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="783d8-500">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="783d8-501">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-501">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="783d8-502">Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="783d8-502">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="783d8-503">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="783d8-503">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="783d8-504">Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="783d8-504">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="783d8-505">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .</span><span class="sxs-lookup"><span data-stu-id="783d8-505">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="783d8-506">Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für bereitstellen <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="783d8-506">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="783d8-507">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="783d8-507">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="783d8-508">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="783d8-508">Bind an array to a class</span></span>

<span data-ttu-id="783d8-509">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="783d8-509">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="783d8-510">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="783d8-510">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="783d8-511">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="783d8-511">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="783d8-512">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="783d8-512">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="783d8-513">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="783d8-513">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="783d8-514">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="783d8-514">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="783d8-515">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="783d8-515">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="783d8-516">Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken*reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="783d8-516">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="783d8-517">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="783d8-517">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="783d8-518">Key</span><span class="sxs-lookup"><span data-stu-id="783d8-518">Key</span></span> | <span data-ttu-id="783d8-519">Wert</span><span class="sxs-lookup"><span data-stu-id="783d8-519">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="783d8-520">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="783d8-520">Reload secrets</span></span>

<span data-ttu-id="783d8-521">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-521">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="783d8-522">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="783d8-522">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="783d8-523">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="783d8-523">Disabled and expired secrets</span></span>

<span data-ttu-id="783d8-524">Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="783d8-524">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="783d8-525">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="783d8-525">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="783d8-526">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="783d8-526">Troubleshoot</span></span>

<span data-ttu-id="783d8-527">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="783d8-527">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="783d8-528">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="783d8-528">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="783d8-529">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="783d8-529">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="783d8-530">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="783d8-530">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="783d8-531">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="783d8-531">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="783d8-532">Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="783d8-532">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="783d8-533">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="783d8-533">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="783d8-534">Die APP hat den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="783d8-534">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="783d8-535">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="783d8-535">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="783d8-536">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="783d8-536">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="783d8-537">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="783d8-537">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="783d8-538">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-538">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="783d8-539">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="783d8-539">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="783d8-540">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="783d8-540">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="783d8-541">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="783d8-541">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="783d8-542">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="783d8-542">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="783d8-543">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="783d8-543">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

