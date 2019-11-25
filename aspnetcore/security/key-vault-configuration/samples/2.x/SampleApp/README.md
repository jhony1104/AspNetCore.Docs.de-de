# <a name="key-vault-configuration-provider-sample-app"></a>Beispiel-App für Key Vault-Konfigurations Anbieter

In diesem Beispiel wird die Verwendung des Azure Key Vault Konfigurations Anbieters veranschaulicht.

Das Beispiel wird in einem von zwei Modi ausgeführt, die durch die `#define`-Anweisung am Anfang der *Program.cs* -Datei bestimmt werden. Anweisungen hierzu finden Sie unter [Präprozessordirektiven im Beispielcode](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate` &ndash; veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf Geheimnisse, die in Azure Key Vault gespeichert sind. Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.
* `Managed` &ndash; veranschaulicht, wie die Azure- [verwaltete Dienstidentität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) zum Authentifizieren der APP für Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen im Code oder in der Konfiguration der APP verwendet wird. Eine Azure AD Client-ID und ein geheimer Schlüssel sind nicht erforderlich, damit sich die APP bei Azure Key Vault authentifizieren muss. Dieses Beispiel muss bereitgestellt werden, um die verwaltete Identität "scearnio" untersuchen zu können Azure App Service.

Weitere Informationen finden Sie unter [Azure Key Vault-Konfigurations Anbieters](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
