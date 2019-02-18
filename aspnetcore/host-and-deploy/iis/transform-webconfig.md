---
title: Transformieren von web.config
author: guardrex
description: Erfahren Sie, wie Sie die Datei „web.config“ beim Veröffentlichen einer ASP.NET Core-App transformieren.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: bd8cf7d8515e874eefd2c326727f56d0a4b502a7
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248602"
---
# <a name="transform-webconfig"></a>Transformieren von web.config

Von [Vijay Ramakrishnan](https://github.com/vijayrkn) und [Luke Latham](https://github.com/guardrex)

Transformationen der *web.config*-Datei können automatisch angewendet werden, wenn eine App veröffentlicht wird basierend auf:

* [Buildkonfiguration](#build-configuration)
* [Profil](#profile)
* [Umgebung](#environment)
* [Benutzerdefiniert](#custom)

Diese Transformationen kommen bei den folgenden *web.config*-Generierungsszenarien vor:

* Automatisch generiert vom `Microsoft.NET.Sdk.Web`-SDK.
* Durch den Entwickler im Inhaltsstammverzeichnis der App bereitgestellt.

## <a name="build-configuration"></a>Buildkonfiguration

Transformationen der Buildkonfiguration werden zuerst ausgeführt.

Beziehen Sie eine *web.{KONFIGURATION}.config*-Datei für jede [Buildkonfiguration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) ein, die eine *web.config*-Transformation erfordert.

Im folgenden Beispiel wird eine konfigurationsspezifische Umgebungsvariable in *web.Release.config* festgelegt:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Die Transformation wird angewendet, wenn die Konfiguration auf *Release* festgelegt ist:

```console
dotnet publish --configuration Release
```

Die MSBuild-Eigenschaft für die Konfiguration ist `$(Configuration)`.

## <a name="profile"></a>Profile

Profiltransformationen werden als Zweites nach den Transformationen der [Buildkonfiguration](#build-configuration) ausgeführt.

Beziehen Sie eine *web.{PROFIL}.config*-Datei für jede Profilkonfiguration ein, die eine *web.config*-Transformation erfordert.

Im folgenden Beispiel wird eine profilspezifische Umgebungsvariable in *web.FolderProfile.config* für ein Ordnerveröffentlichungsprofil festgelegt:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Die Transformation wird bei dem Profil *FolderProfile* angewendet:

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

Die MSBuild-Eigenschaft für den Profilnamen ist `$(PublishProfile)`.

Wenn kein Profil übergeben wird, lautet der Standardname für das Profil **FileSystem**, und *web.FileSystem.config* wird angewendet, wenn die Datei im Inhaltsstammverzeichnis der App vorhanden ist.

## <a name="environment"></a>Umgebung

Umgebungstransformationen werden als Drittes nach den Transformationen von [Buildkonfiguration](#build-configuration) und [Profil](#profile) ausgeführt.

Beziehen Sie eine *web.{UMGEBUNG}.config*-Datei für jede [Umgebung](xref:fundamentals/environments) ein, die eine *web.config*-Transformation erfordert.

Im folgenden Beispiel wird eine umgebungsspezifische Umgebungsvariable in *web.Production.config* für die Produktionsumgebung festgelegt:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Die Transformation wird angewendet, wenn die Umgebung *Production* ist:

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

Die MSBuild-Eigenschaft für die Umgebung ist `$(EnvironmentName)`.

Informationen zum Veröffentlichen von Visual Studio aus und Verwenden eines Veröffentlichungsprofils finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

Die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable wird automatisch der *web.config*-Datei hinzugefügt, wenn der Name der Umgebung angegeben wird.

## <a name="custom"></a>Benutzerdefiniert

Benutzerdefinierte Transformationen werden als Letztes nach den Transformationen von [Buildkonfiguration](#build-configuration), [Profil](#profile) und [Umgebung](#environment) ausgeführt.

Beziehen Sie eine *{BENUTZERDEFINIERTER_NAME}.transform*-Datei für jede benutzerdefinierte Konfiguration ein, die eine *web.config*-Transformation erfordert.

Im folgenden Beispiel wird eine benutzerdefinierte Transformationsumgebungsvariable in *custom.transform* festgelegt:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

Die Transformation wird angewendet, wenn die `CustomTransformFileName`-Eigenschaft dem [dotnet publish](/dotnet/core/tools/dotnet-publish)-Befehl übergeben wird:

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

Die MSBuild-Eigenschaft für den Profilnamen ist `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Verhindern der web.config-Transformation

Um Transformationen der *web.config*-Datei zu verhindern, legen Sie die MSBuild-Eigenschaft `$(IsWebConfigTransformDisabled)` fest:

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Syntax der Web.config-Transformation für die Bereitstellung von Webanwendungsprojekten](http://go.microsoft.com/fwlink/?LinkId=301874)
* [Syntax der Web.config-Transformation für die Bereitstellung von Webanwendungsprojekten mit Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))
