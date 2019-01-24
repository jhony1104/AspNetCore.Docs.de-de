---
title: Verzeichnisstruktur für ASP.NET Core
author: guardrex
description: Erfahren Sie mehr über die Verzeichnisstruktur veröffentlichter ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2018
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 4bc5ead8e24c4bb7fe6cd2f52fd2aa622187180c
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341393"
---
# <a name="aspnet-core-directory-structure"></a>Verzeichnisstruktur für ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Das Verzeichnis *publish* enthält die zur App gehörenden bereitstellbaren Ressourcen, die mit dem [dotnet publish](/dotnet/core/tools/dotnet-publish)-Befehl erstellt wurden. Das Verzeichnis enthält:

* Anwendungsdateien
* Konfigurationsdateien
* Statische Ressourcen
* Pakete
* Eine Runtime (nur [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd))

| App-Typ | Verzeichnisstruktur |
| -------- | ------------------- |
| [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li>publish&dagger;<ul><li>Logs&dagger; (optional, es sei denn, es müssen stdout-Protokolle empfangen werden)</li><li>Ansichten&dagger; (MVC-Apps, wenn Ansichten nicht vorkompiliert sind)</li><li>Seiten&dagger; (MVC- oder Razor Pages-Apps, wenn Seiten nicht vorkompiliert sind)</li><li>wwwroot&dagger;</li><li>*\.DLL-Dateien</li><li>{ASSEMBLYNAME}.deps.json</li><li>{ASSEMBLYNAME}.dll</li><li>{ASSEMBLYNAME}.pdb</li><li>{ASSEMBLYNAME}.Views.dll</li><li>{ASSEMBLYNAME}.Views.pdb</li><li>{ASSEMBLYNAME}.runtimeconfig.json</li><li>web.config (IIS-Bereitstellungen)</li></ul></li></ul> |
| [Eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Logs&dagger; (optional, es sei denn, es müssen stdout-Protokolle empfangen werden)</li><li>Ansichten&dagger; (MVC-Apps, wenn Ansichten nicht vorkompiliert sind)</li><li>Seiten&dagger; (MVC- oder Razor Pages-Apps, wenn Seiten nicht vorkompiliert sind)</li><li>wwwroot&dagger;</li><li>\*DLL-Dateien</li><li>{ASSEMBLYNAME}.deps.json</li><li>{ASSEMBLYNAME}.dll</li><li>{ASSEMBLYNAME}.exe</li><li>{ASSEMBLYNAME}.pdb</li><li>{ASSEMBLYNAME}.Views.dll</li><li>{ASSEMBLYNAME}.Views.pdb</li><li>{ASSEMBLYNAME}.runtimeconfig.json</li><li>web.config (IIS-Bereitstellungen)</li></ul></li></ul> |

&dagger;Gibt ein Verzeichnis an

Das Verzeichnis *publish* stellt den *Pfad des Inhaltsstammverzeichnisses* (auch als *Pfad der Anwendungsbasis* bekannt) der Bereitstellung dar. Unabhängig vom Namen des Verzeichnisses *publish* der auf dem Server bereitgestellten App dient der Speicherort des Verzeichnisses als physischer Pfad des Servers für die gehostete App.

Das Verzeichnis *wwwroot* enthält, sofern vorhanden, nur statische Objekte.

Das Verzeichnis *Logs* (Protokolle) kann mit einem der folgenden beiden Ansätze für die Bereitstellung erstellt werden:

* Fügen Sie das folgende `<Target>`-Element zur Projektdatei hinzu:

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   Mit dem `<MakeDir>`-Element wird in der veröffentlichten Ausgabe ein leerer *Logs*-Ordner erstellt. Das Element bestimmt mithilfe der Eigenschaft `PublishDir` den Zielspeicherort für die Erstellung des Ordners. Mehrere Bereitstellungsmethoden, wie z.B. Web Deploy, überspringen leere Ordner während der Bereitstellung. Das Element `<WriteLinesToFile>` generiert im Ordner *Logs* eine Datei, die eine Bereitstellung des Ordners auf dem Server garantiert. Eine Ordnererstellung mit dieser Vorgehensweise schlägt fehl, wenn der Workerprozess keinen Schreibzugriff auf den Zielordner hat.

* Erstellen Sie das Verzeichnis *Logs* physisch auf dem Server in der Bereitstellung.

Für das Bereitstellungsverzeichnis sind Lese-/Ausführungsberechtigungen erforderlich. Für das Verzeichnis *Logs* sind Lese-/Schreibberechtigungen erforderlich. Für weitere Verzeichnisse, in die Dateien geschrieben werden, sind Lese-/Schreibberechtigungen erforderlich.

Für die [stdout-Protokollierung für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ist kein *Logs*-Ordner in der Bereitstellung erforderlich. Mit dem Modul können beliebige Ordner im `stdoutLogFile`-Pfad erstellt werden, wenn die Protokolldatei erstellt wird. Das Erstellen eines *Logs*-Ordners ist für die [erweiterte Debugprotokollierung für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nützlich. Ordner im Pfad, die für den `<handlerSetting>`-Wert bereitgestellt werden, werden nicht automatisch vom Modul erstellt und müssen zuvor in der Bereitstellung vorhanden sein, damit das Modul das Debugprotokoll schreiben kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/)
* [Zielframeworks](/dotnet/standard/frameworks)
* [.NET Core-RID-Katalog](/dotnet/core/rid-catalog)
