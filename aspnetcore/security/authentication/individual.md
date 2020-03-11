---
title: Artikel, die auf ASP.net Core Projekten basieren, die mit einzelnen Benutzerkonten erstellt wurden
author: rick-anderson
description: Entdecken Sie Artikel, die auf ASP.net Core Projekte basieren, die mit einzelnen Benutzerkonten erstellt wurden.
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651955"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Artikel, die auf ASP.net Core Projekten basieren, die mit einzelnen Benutzerkonten erstellt wurden

ASP.net Core Identität ist in Projektvorlagen in Visual Studio mit der Option "einzelne Benutzerkonten" enthalten.

Die Authentifizierungs Vorlagen sind in .net Core-CLI mit `-au Individual`verfügbar:

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Sehen Sie sich [dieses GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/5833) bei der Web-API-Authentifizierung

<a name="no"></a>

## <a name="no-authentication"></a>Keine Authentifizierung

Die Authentifizierung wird im .net Core-CLI mit der `-au`-Option angegeben. In Visual Studio ist das Dialogfeld **Authentifizierung ändern** für neue Webanwendungen verfügbar. Der Standardwert für neue Web-Apps in Visual Studio ist **keine Authentifizierung**.

Ohne Authentifizierung erstellte Projekte:

* Es sind keine Webseiten und Benutzeroberflächen zum Anmelden und Abmelden enthalten.
* Keinen Authentifizierungscode enthalten.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows-Authentifizierung

Die Windows-Authentifizierung wird für neue Web-Apps im .net Core-CLI mit der `-au Windows`-Option angegeben. Im Dialogfeld **Authentifizierung ändern** werden in Visual Studio die Optionen für die **Windows-Authentifizierung** angezeigt.

Wenn die Windows-Authentifizierung ausgewählt ist, wird die APP für die Verwendung des [IIS-Moduls der Windows-Authentifizierung](xref:host-and-deploy/iis/modules)konfiguriert. Die Windows-Authentifizierung ist für Intranetwebsites vorgesehen.

## <a name="dotnet-new-webapp-authentication-options"></a>Optionen für die neue webapp-Authentifizierung in dotnet

In der folgenden Tabelle sind die für neue Web-Apps verfügbaren Authentifizierungs Optionen aufgeführt:

| Option | Authentifizierungstyp | Link für weitere Informationen |
 | ----------------- | ------------ | ---------- |
| Keine            |  Keine Authentifizierung | | 
| Einzelkonto      |  Individuelle Authentifizierung | <xref:security/authentication/identity>
| IndividualB2C   |  In der Cloud gehostete individuelle Authentifizierung mit Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Singleorg       |  Organisations Authentifizierung für einen einzelnen Mandanten | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Multiorg        |  Organisations Authentifizierung für mehrere Mandanten | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows-Authentifizierung | [Windows-Authentifizierung](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio-Optionen für die neue webapp-Authentifizierung

In der folgenden Tabelle sind die Authentifizierungs Optionen aufgeführt, die beim Erstellen einer neuen Web-App mit Visual Studio verfügbar sind:

| Option | Authentifizierungstyp | Link für weitere Informationen |
 | ----------------- | ------------ | ---------- |
| Keine            |  Keine Authentifizierung | | 
| Einzelne Benutzerkonten/Benutzerkonten in-APP speichern |  Individuelle Authentifizierung | <xref:security/authentication/identity> |
| Einzelne Benutzerkonten/Verbindung mit einem vorhandenen Benutzerspeicher in der Cloud |  In der Cloud gehostete individuelle Authentifizierung mit Azure AD B2C | [Azure AD B2C](/azure/active-directory-b2c/) |
| Geschäfts-, Schul-oder Uni-Cloud/Single org  |  Organisations Authentifizierung für einen einzelnen Mandanten | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Geschäfts-, Schul-oder Uni-Cloud/Multiple org |  Organisations Authentifizierung für mehrere Mandanten | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows-Authentifizierung | [Windows-Authentifizierung](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In den folgenden Artikeln wird gezeigt, wie Sie den in ASP.net Core Vorlagen generierten Code verwenden, der einzelne Benutzerkonten verwendet:

* [Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)](xref:security/authentication/accconfirm)
* [Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten](xref:security/authorization/secure-data)
