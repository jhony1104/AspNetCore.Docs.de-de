---
ms.openlocfilehash: 2fe12027e7a5233cf01e6c412f7ee536d479facd
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665787"
---
<!--Don't update this for 2.2, use the 2.2 version --> Der Aufruf von [AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity) konfiguriert die Einstellungen der Standard-Schema. Die [AddAuthentication(String)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_String_) überladen, legt die [DefaultScheme](/dotnet/api/microsoft.aspnetcore.authentication.authenticationoptions.defaultscheme) Eigenschaft. Die [AddAuthentication (Aktion&lt;authenticationoptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) Überladung ermöglicht das Konfigurieren von Authentifizierungsoptionen, die zum Einrichten von standardmäßigen Authentifizierungsschemas für unterschiedliche Zwecke verwendet werden kann. Nachfolgende Aufrufe von `AddAuthentication` außer Kraft setzen, die zuvor konfigurierten [authenticationoptions](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions) Eigenschaften.

[AuthenticationBuilder](/dotnet/api/microsoft.aspnetcore.authentication.authenticationbuilder) Erweiterungsmethoden, die registriert einen authentifizierungshandler für die können nur einmal pro Authentifizierungsschema aufgerufen werden. Überladungen vorhanden sind, mit denen die Konfiguration von Eigenschaften für Schema, Name des Schemas und Anzeigename.
