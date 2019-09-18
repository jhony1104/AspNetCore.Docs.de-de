---
title: Artikel, die auf ASP.net Core Projekten basieren, die mit einzelnen Benutzerkonten erstellt wurden
author: rick-anderson
description: Entdecken Sie Artikel, die auf ASP.net Core Projekte basieren, die mit einzelnen Benutzerkonten erstellt wurden.
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: cf548417268a8587787471b9ed91c0ed109fbee9
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080703"
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

Sehen Sie sich [dieses GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/5833) bei der Web-API-Authentifizierung

<a name="no"></a>

## <a name="no-authentication"></a>Keine Authentifizierung

Die Authentifizierung wird im .net Core-CLI mit der `-au` Option angegeben. In Visual Studio ist das Dialogfeld **Authentifizierung ändern** für neue Webanwendungen verfügbar. Der Standardwert für neue Web-Apps in Visual Studio ist **keine Authentifizierung**.

Ohne Authentifizierung erstellte Projekte:

* Es sind keine Webseiten und Benutzeroberflächen zum Anmelden und Abmelden enthalten.
* Keinen Authentifizierungscode enthalten.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows-Authentifizierung

Die Windows-Authentifizierung wird für neue Web-Apps im .net Core-CLI mit `-au Windows` der Option angegeben. Im Dialogfeld **Authentifizierung ändern** werden in Visual Studio die Optionen für die **Windows-Authentifizierung** angezeigt.

Wenn die Windows-Authentifizierung ausgewählt ist, wird die APP für die Verwendung des [IIS-Moduls der Windows-Authentifizierung](xref:host-and-deploy/iis/modules)konfiguriert. Die Windows-Authentifizierung ist für Intranetwebsites vorgesehen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

In den folgenden Artikeln wird gezeigt, wie Sie den in ASP.net Core Vorlagen generierten Code verwenden, der einzelne Benutzerkonten verwendet:

* [Zweistufige Authentifizierung mit SMS](xref:security/authentication/2fa)
* [Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)](xref:security/authentication/accconfirm)
* [Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten](xref:security/authorization/secure-data)
