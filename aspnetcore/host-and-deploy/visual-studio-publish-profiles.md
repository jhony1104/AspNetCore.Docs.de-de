---
title: Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps
author: rick-anderson
description: Informationen zum Erstellen von Veröffentlichungsprofilen in Visual Studio und zu deren Verwendung zum Verwalten von Bereitstellungen von ASP.NET Core-Apps an verschiedene Ziele.
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 03acaa73fc2ebdc62522a1e081ca6ed72515483f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836488"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a>Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps

Von [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="<= aspnetcore-1.1"

Laden Sie für die 1.1-Version dieses Themas das Dokument [Visual Studio publish profiles for ASP.NET Core app deployment (Version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf) herunter.

::: moniker-end

Dieser Artikel konzertiert sich auf die Verwendung von Visual Studio 2017 oder höher zum Erstellen von Veröffentlichungsprofilen. Die Veröffentlichungsprofile, die mit Visual Studio erstellt werden, können von MSBuild und Visual Studio ausgeführt werden. Anweisungen zum Veröffentlichen in Azure finden Sie unter [Veröffentlichen einer ASP.NET Core-Web-App in Azure App Service mit Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs).

Die folgende Projektdatei wurde mit dem Befehl `dotnet new mvc` erstellt:

::: moniker range=">= aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

Das Attribut `Sdk` des `<Project>`-Elements führt die folgenden Tasks durch:

* Es importiert am Anfang die Eigenschaftendatei aus *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props*.
* Es importiert am Ende die Zieledatei von *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*.

Der Standardspeicherort für `MSBuildSDKsPath` (mit Visual Studio 2017 Enterprise) ist der Ordner *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.

Das `Microsoft.NET.Sdk.Web` SDK hängt von Folgendem ab:

* *Microsoft.NET.Sdk.Web.ProjectSystem*
* *Microsoft.NET.Sdk.Publish*

Dadurch werden die folgenden Eigenschaften und Ziele importiert:

* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*
* *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*

Veröffentlichungsziele importieren basierend auf der verwendeten Veröffentlichungsmethode die richtige Gruppe von Zielen.

Wenn in MSBuild oder Visual Studio ein Projekt geladen wird, werden die folgenden Aktionen auf hoher Ebene ausgeführt:

* Erstellen des Projekts
* Berechnen der zu veröffentlichenden Dateien
* Veröffentlichen der Dateien auf dem Ziel

## <a name="compute-project-items"></a>Berechnen der Projektelemente

Wenn das Projekt geladen ist, werden die Projektelemente (Dateien) berechnet. Die `item type`-Attribut bestimmt, wie die Datei verarbeitet wird. Standardmäßig sind *CS*-Dateien in der `Compile`-Elementliste enthalten. Dateien in der `Compile`-Elementliste werden kompiliert.

Die `Content`-Elementliste enthält Dateien, die zusätzlich zu den Buildausgaben veröffentlicht werden. Standardmäßig sind Dateien, die mit dem Muster `wwwroot/**` übereinstimmen, im Element `Content` enthalten. Das `wwwroot/\*\*` [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns) stimmt mit allen Dateien im *wwwroot*-Ordner **und**  in den Unterordnern überein. Um eine Datei explizit der Veröffentlichungsliste hinzuzufügen, fügen Sie die Datei wie in [Includedateien](#include-files) gezeigt direkt der *CSPROJ*-Datei hinzu.

Wenn Sie auf die Schaltfläche **Veröffentlichen** in Visual Studio klicken oder aus der Befehlszeile veröffentlichen:

* Die Eigenschaften und Elemente werden berechnet (die Dateien, die für den Build benötigt werden).
* **Nur Visual Studio**: NuGet-Pakete werden wiederhergestellt. (Die Wiederherstellung muss explizit vom Benutzer auf der CLI durchgeführt werden.)
* Das Projekt wird erstellt.
* Die zu veröffentlichenden Elemente werden berechnet (die Dateien, die für die Veröffentlichung benötigt werden).
* Das Projekt wird veröffentlicht (die berechneten Dateien werden in das Veröffentlichungsziel kopiert).

Wenn ein ASP.NET Core-Projekt auf `Microsoft.NET.Sdk.Web` in der Projektdatei verweist, wird eine *app_offline.htm*-Datei im Stammverzeichnis des Verzeichnisses der Web-App abgelegt. Wenn die Datei vorhanden ist, fährt das ASP.NET Core Module die App ordnungsgemäß herunter und verarbeitet die Datei *app_offline.htm* während der Bereitstellung. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Grundlegendes zur Veröffentlichung über die Befehlszeile

Die Veröffentlichung über die Befehlszeile funktioniert auf allen von .NET Core unterstützten Plattformen und setzt Visual Studio nicht voraus. In den unten stehenden Beispielen wird der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) über das Projektverzeichnis ausgeführt (das die Datei *CSPROJ* enthält). Wenn Sie nicht im Projektordner sind, können Sie explizit in den Projektdateipfad übergeben. Beispiel:

```console
dotnet publish C:\Webs\Web1
```

Führen Sie die folgenden Befehle zum Erstellen und Veröffentlichen eine Web-App aus:

```console
dotnet new mvc
dotnet publish
```

Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) erzeugt eine Ausgabe, die der folgenden ähnelt:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

Der Standardveröffentlichungsordner ist `bin\$(Configuration)\netcoreapp<version>\publish`. Der Standardwert für `$(Configuration)` ist *Debuggen*. Im vorgehenden Beispiel ist `<TargetFramework>` gleich `netcoreapp2.0`.

`dotnet publish -h` zeigt Hilfeinformationen zum Veröffentlichen an.

Der folgende Befehl gibt einen `Release`-Build und das Veröffentlichungsverzeichnis an:

```console
dotnet publish -c Release -o C:\MyWebs\test
```

Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) ruft MSBuild auf, das das `Publish`-Ziel aufruft. Alle Parameter, die an `dotnet publish` übergeben werden, werden an MSBuild übergeben. Der Parameter `-c` wird der MSBuild-Eigenschaft `Configuration` zugeordnet. Der Parameter `-o` wird `OutputPath` zugeordnet.

MSBuild-Eigenschaften können mithilfe eines der folgenden Formate übergeben werden:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Der folgende Befehl veröffentlicht einen `Release`-Build auf einer Netzwerkfreigabe:

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

Die Netzwerkfreigabe wird durch führende Schrägstriche (*//r8/*) angegeben und funktioniert auf allen Plattformen, die von .NET Core unterstützt werden.

Versichern Sie sich, dass die veröffentlichte App für die Bereitstellung nicht ausgeführt wird. Die Dateien im Ordner *publish* sind gesperrt, wenn die App ausgeführt wird. Die Bereitstellung kann nicht erfolgen, da die gesperrten Dateien nicht kopiert werden können.

## <a name="publish-profiles"></a>Veröffentlichungsprofile

In diesem Abschnitt wird Visual Studio 2017 oder höher verwendet, um ein Veröffentlichungsprofil zu erstellen. Nachdem das Profil erstellt ist, ist Veröffentlichung aus Visual Studio oder über die Befehlszeile möglich.

Veröffentlichungsprofile können den Veröffentlichungsprozess vereinfachen, und eine beliebige Anzahl von Profilen kann vorhanden sein. Erstellen Sie ein Veröffentlichungsprofil in Visual Studio durch Auswahl eines der folgenden Pfade:

* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
* Wählen Sie **{PROJEKTNAME} veröffentlichen** im Menü **Build** aus.

Die Registerkarte **Veröffentlichen** der Seite für Appfunktionen wird angezeigt. Wenn das Projekt kein Veröffentlichungsprofil enthält, wird die folgende Seite angezeigt:

![Die Registerkarte „Veröffentlichen“ der Seite für Appfunktionen wird angezeigt](visual-studio-publish-profiles/_static/az.png)

Wenn **Ordner** ausgewählt ist, geben Sie einen Ordnerpfad ein, um die veröffentlichten Objekte zu speichern. Der Standardordner ist *bin\Release\PublishOutput*. Klicken Sie auf die Schaltfläche **Profil erstellen**, um den Vorgang abzuschließen.

Sobald ein Veröffentlichungsprofil erstellt wurde, ändert sich die Registerkarte **Veröffentlichen**. Das neu erstellte Profil wird in einer Dropdownliste angezeigt. Klicken Sie auf **Neues Profil erstellen**, um ein anderes neues Profil zu erstellen.

![Die Registerkarte „Veröffentlichen“ der Seite für Appfunktionen zeigt das Ordnerprofil an](visual-studio-publish-profiles/_static/create_new.png)

Der Webpublishing-Assistent unterstützt folgende Veröffentlichungsziele:

* Azure App Service
* Azure Virtual Machines
* IIS, FTP usw. (für alle Webserver)
* Ordner
* Profil importieren

Weitere Informationen finden Sie unter [Welche Optionen für die Veröffentlichung sind für mich geeignet?](/visualstudio/ide/not-in-toc/web-publish-options).

Beim Erstellen eines Veröffentlichungsprofils mit Visual Studio wird eine *Properties/PublishProfiles/{PROFILNAME}.pubxml*-MSBuild-Datei erstellt. Diese *PUBXML*-Datei ist eine MSBuild-Datei und enthält die Konfigurationseinstellungen für die Veröffentlichung. Die Datei kann geändert werden, um den Build- und Veröffentlichungsprozess anzupassen. Diese Datei wird vom Veröffentlichungsprozess gelesen. `<LastUsedBuildConfiguration>` ist ein Sonderfall, da es sich dabei um eine globale Eigenschaft handelt, die nicht in einer Datei enthalten sein sollte, die in den Build importiert wurde. Weitere Informationen finden Sie unter [MSBuild: how to set the configuration property (MSBuild: Festlegen der Konfigurationseigenschaft)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

Beim Veröffentlichen in einem Azure-Ziel enthält die *PUBXML*-Datei Ihre Azure-Abonnement-ID. Bei diesem Zieltyp wird vom Hinzufügen der Datei zur Quellkontrolle abgeraten. Beim Veröffentlichen auf einem Nicht-Azure-Ziel kann die *PUBXML*-Datei eingecheckt werden.

Vertrauliche Informationen (z.B. das Kennwort für die Veröffentlichung) werden pro Benutzer/Computer verschlüsselt. Sie werden in der Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml.user* gespeichert. Da diese Datei vertrauliche Informationen enthalten kann, sollte sie nicht in die Quellverwaltung eingecheckt werden.

Einen Überblick über das Veröffentlichen einer Web-App auf ASP.NET Core finden Sie unter [Hosten und Bereitstellen](xref:host-and-deploy/index). Die MSBuild-Aufgaben und -Ziele, die zum Veröffentlichen einer ASP.NET Core-App erforderlich sind, werden als Open Source in https://github.com/aspnet/websdk bereitgestellt.

`dotnet publish` kann den Ordner, MSDeploy und [Kudu](https://github.com/projectkudu/kudu/wiki)-Veröffentlichungsprofile verwenden:

Ordner (funktioniert plattformübergreifend):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

MSDeploy (funktioniert zurzeit nur in Windows, da MSDdeploy nicht plattformübergreifend ist):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

MSDeploy-Paket (funktioniert zurzeit nur in Windows, da MSDdeploy nicht plattformübergreifend ist):

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

Übergeben Sie in den vorherigen Beispielen `deployonbuild` **nicht** an `dotnet publish`.

Weitere Informationen finden Sie unter [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).

`dotnet publish` unterstützt Kudu-APIs zur Veröffentlichung in Azure von jeder Plattform aus. Die Visual Studio-Veröffentlichung unterstützt die Kudu-APIs, wird für die plattformübergreifende Veröffentlichung in Azure aber vom WebSDK unterstützt.

Fügen Sie dem Ordner *Properties/PublishProfiles* ein Veröffentlichungsprofil mit folgendem Inhalt hinzu:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

Führen Sie den folgenden Befehl aus, um die Veröffentlichungsinhalte zu zippen und über die Kudu-APIs in Azure zu veröffentlichen:

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

Legen Sie die folgenden MSBuild-Eigenschaften fest, wenn Sie ein Veröffentlichungsprofil verwenden:

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

Beispielsweise können Sie einen der unten stehenden Befehle ausführen, wenn Sie mit einem Profil namens *FolderProfile* veröffentlichen:

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

Beim Aufrufen von [dotnet build](/dotnet/core/tools/dotnet-build) wird `msbuild` aufgerufen, um den Build- und Veröffentlichungsprozess auszuführen. Der Aufruf von `dotnet build` oder `msbuild` ist bei Übergabe in einem Ordnerprofil gleichwertig. Wenn Sie MSBuild direkt in Windows aufrufen, wird die .NET Framework-Version von MSBuild verwendet. Die Veröffentlichung mit MSDeploy ist aktuell auf Windows-Computer beschränkt. Wenn Sie `dotnet build` von einem Profil ohne Ordner aufrufen, wird MSBuild aufgerufen. MSBuild verwendet MSDeploy für Profile ohne Ordner. Wenn Sie `dotnet build` von einem Profil ohne Ordner aufrufen, wird MSBuild (mithilfe von MSDeploy) aufgerufen. Dies führt zu einem Fehler (auch beim Ausführen auf einer Windows-Plattform). Rufen Sie MSBuild direkt auf, um mit einem Profil ohne Ordner zu veröffentlichen.

Der folgende Ordner „Veröffentlichungsprofil“ wurde mit Visual Studio erstellt und veröffentlicht in eine Netzwerkfreigabe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Beachten Sie, dass `<LastUsedBuildConfiguration>` auf `Release` festgelegt ist. Bei der Veröffentlichung von Visual Studio wird der `<LastUsedBuildConfiguration>`-Wert für die Konfigurationseigenschaft mithilfe des Werts festgelegt, der beim Starten des Veröffentlichungsprozesses vorliegt. Die `<LastUsedBuildConfiguration>`-Konfigurationseigenschaft ist ein Sonderfall und sollte nicht in einer importierte MSBuild-Datei überschrieben werden. Diese Eigenschaft kann von der Befehlszeile aus überschrieben werden.

Mit der .NET Core-CLI:

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Verwenden von MSBuild:

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Veröffentlichen auf einen MSDeploy-Endpunkt von der Befehlszeile

Im folgenden Beispiel wird von Visual Studio eine ASP.NET Core-Web-App namens *AzureWebApp* erstellt. Ein Azure Apps-Veröffentlichungsprofil wird mit Visual Studio hinzugefügt. Weitere Informationen zum Erstellen eines Profils finden Sie unter im Abschnitt [Veröffentlichungsprofile](#publish-profiles).

Um eine App mit einem Veröffentlichungsprofil bereitzustellen, führen Sie den Befehl `msbuild` an einer **Developer-Eingabeaufforderung** von Visual Studio aus. Die Eingabeaufforderung finden Sie im Ordner *Visual Studio* im **Start**-Menü auf der Windows-Taskleiste. Für einfacheren Zugriff können Sie die Eingabeaufforderung zum Menü **Tools** in Visual Studio hinzufügen. Weitere Informationen finden Sie unter [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild hat die folgende Befehlssyntax:

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* {PATH}: der Pfad zur Projektdatei der App.
* {PROFILE}: der Name des Veröffentlichungsprofils.
* {USERNAME}: der MSDeploy-Benutzername. Die {USERNAME} ist im Veröffentlichungsprofil zu finden.
* {PASSWORD}: das MSDeploy-Kennwort. Sie finden das {PASSWORD} in der Datei *{PROFILE}.PublishSettings*. Laden Sie die Datei *.PublishSettings* von einem der folgenden Speicherorte herunter:
  * Projektmappen-Explorer: Wählen Sie **Ansicht** > **Cloud-Explorer** aus. Stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Öffnen Sie **App Services**. Klicken Sie mit der rechten Maustaste auf die App. Wählen Sie **Veröffentlichungsprofil herunterladen** aus.
  * Azure-Portal: Wählen Sie **Veröffentlichungsprofil abrufen** im Bereich **Übersicht** der Web-App aus.

Im folgenden Beispiel wird ein Veröffentlichungsprofil namens *AzureWebApp – Web Deploy* verwendet:

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Ein Veröffentlichungsprofil kann auch mit dem .NET Core-CLI-Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) an einer Windows-Eingabeaufforderung verwendet werden:

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> Der Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) ist plattformübergreifend verfügbar und kann ASP.NET Core-Apps unter macOS und Linux kompilieren. Allerdings ist es mit MSBuild unter macOS und Linux nicht möglich, eine App in Azure oder in einem anderen MSDeploy-Endpunkt bereitzustellen. MSDeploy ist nur unter Windows verfügbar.

## <a name="set-the-environment"></a>Festlegen der Umgebung

Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein, um die [Umgebung](xref:fundamentals/environments) der App festzulegen:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

## <a name="exclude-files"></a>Dateien ausschließen

Beim Veröffentlichen von ASP.NET Core-Apps sind die Buildartefakte und die Inhalte des Ordners *wwwroot* enthalten. `msbuild` unterstützt [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns). Das folgende `<Content>`-Element beispielsweise schließt alle Textdateien (*TXT*) aus dem Ordner *wwwroot/content* und all seinen Unterordnern aus.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Das vorstehende Markup kann einem Veröffentlichungsprofil oder der *CSPROJ*-Datei hinzugefügt werden. Wenn es zu der *CSPROJ*-Datei hinzugefügt wird, wird die Regel zu allen Veröffentlichungsprofilen im Projekt hinzugefügt.

Das folgende `<MsDeploySkipRules>`-Element schließt alle Dateien aus dem Ordner *wwwroot/content* aus:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` löscht die *Skip*-Ziele von der Bereitstellungsseite nicht. `<Content>`-Zieldateien und -ordner werden von der Bereitstellungsseite gelöscht. Nehmen wir beispielsweise an, dass eine bereitgestellte Web-App folgende Dateien enthält:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Wenn folgende `<MsDeploySkipRules>`-Elemente hinzugefügt werden, würden diese Dateien nicht von der Bereitstellungsseite gelöscht werden.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Die vorangehenden `<MsDeploySkipRules>`-Elemente verhindern, dass *übersprungene* Dateien bereitgestellt werden. Diese Dateien werden nach ihrer Bereitstellung nicht gelöscht.

Das folgende `<Content>`-Element löscht die Zieldateien auf der Bereitstellungsseite:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Die Bereitstellung über die Befehlszeile mit dem vorangehenden `<Content>`-Element führt zu folgender Ausgabe:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Includedateien

Das folgende Markup schließt einen Ordner *images* außerhalb des Projektverzeichnisses vom Ordner *wwwroot/images* auf der Veröffentlichungssite ein:

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

Das Markup kann der *CSPROJ*-Datei oder dem Veröffentlichungsprofil hinzugefügt werden. Wenn es der *CSPROJ*-Datei hinzugefügt wird, wird es in jedes Veröffentlichungsprofil im Projekt eingeschlossen.

Das folgende hervorgehobene Markup zeigt Folgendes:

* Kopieren einer Datei von außerhalb des Projekts in den Ordner *wwwroot*.
* Ausschließen des Ordners *wwwroot\Content*.
* Ausschließen von *Views\Home\About2.cshtml*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

Weitere Bereitstellungsbeispiele finden Sie unter [WebSDK Readme (WebSDK-Infodatei)](https://github.com/aspnet/websdk).

## <a name="run-a-target-before-or-after-publishing"></a>Führen Sie ein Ziel vor oder nach der Veröffentlichung aus.

Die integrierten Ziele `BeforePublish` und `AfterPublish` führen ein Ziel vor oder nach dem Veröffentlichungsziel aus. Fügen Sie die folgenden Elemente dem Veröffentlichungsprofil hinzu, um Konsolenmeldungen vor und nach der Veröffentlichung zu protokollieren:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Veröffentlichen auf einem Server unter Verwendung eines nicht vertrauenswürdigen Zertifikats

Fügen Sie die Eigenschaft `<AllowUntrustedCertificate>` mit dem Wert `True` auf dem Veröffentlichungsprofil hinzu:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Der Kudu-Dienst

Verwenden Sie zum Anzeigen der Dateien in einer Web-App-Bereitstellung von Azure App Service den [Kudu-Dienst](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Fügen Sie das Token `scm` an den Namen Ihrer Web-App an. Beispiel:

| URL                                    | Ergebnis       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webanwendung      |
| `http://mysite.scm.azurewebsites.net/` | Der Kudu-Dienst |

Wählen Sie das Menüelement [Debugging-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console), aus, um Dateien anzuzeigen, zu bearbeiten, zu löschen oder hinzuzufügen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) vereinfacht die Bereitstellung von Web-Apps und Websites auf IIS-Servern.
* [https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): Probleme melden und Features für die Bereitstellung anfordern.
* [Veröffentlichen einer ASP.NET-Web-App auf einer Azure-VM aus Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
