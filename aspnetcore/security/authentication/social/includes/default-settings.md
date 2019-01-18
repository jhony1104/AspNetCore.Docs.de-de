---
ms.openlocfilehash: a7be92adffc06ac0f25b84ea15d1a8c4896f4f9d
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54397078"
---
<!--Don't update this for 2.2, use the 2.2 version --> Der Aufruf von [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity) konfiguriert die Einstellungen der Standard-Schema. Die [AddAuthentication(String)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_String_) überladen, legt die [DefaultScheme](/dotnet/api/microsoft.aspnetcore.authentication.authenticationoptions.defaultscheme) Eigenschaft. Die [AddAuthentication (Aktion&lt;authenticationoptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) Überladung ermöglicht das Konfigurieren von Authentifizierungsoptionen, die zum Einrichten von standardmäßigen Authentifizierungsschemas für unterschiedliche Zwecke verwendet werden kann. Nachfolgende Aufrufe von `AddAuthentication` außer Kraft setzen, die zuvor konfigurierten [authenticationoptions](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions) Eigenschaften.

[AuthenticationBuilder](/dotnet/api/microsoft.aspnetcore.authentication.authenticationbuilder) Erweiterungsmethoden, die registriert einen authentifizierungshandler für die können nur einmal pro Authentifizierungsschema aufgerufen werden. Überladungen vorhanden sind, mit denen die Konfiguration von Eigenschaften für Schema, Name des Schemas und Anzeigename.
