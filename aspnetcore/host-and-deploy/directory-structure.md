---
title: Verzeichnisstruktur für ASP.NET Core
author: guardrex
description: Erfahren Sie mehr über die Verzeichnisstruktur veröffentlichter ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: host-and-deploy/directory-structure
ms.openlocfilehash: f1df047decc7a0a6b7dcee57a690c55eea428b05
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166980"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="dcbd9-103">Verzeichnisstruktur für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcbd9-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="dcbd9-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dcbd9-105">Das Verzeichnis *publish* enthält die zur App gehörenden bereitstellbaren Ressourcen, die mit dem [dotnet publish](/dotnet/core/tools/dotnet-publish)-Befehl erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="dcbd9-106">Das Verzeichnis enthält:</span><span class="sxs-lookup"><span data-stu-id="dcbd9-106">The directory contains:</span></span>

* <span data-ttu-id="dcbd9-107">Anwendungsdateien</span><span class="sxs-lookup"><span data-stu-id="dcbd9-107">Application files</span></span>
* <span data-ttu-id="dcbd9-108">Konfigurationsdateien</span><span class="sxs-lookup"><span data-stu-id="dcbd9-108">Configuration files</span></span>
* <span data-ttu-id="dcbd9-109">Statische Ressourcen</span><span class="sxs-lookup"><span data-stu-id="dcbd9-109">Static assets</span></span>
* <span data-ttu-id="dcbd9-110">Pakete</span><span class="sxs-lookup"><span data-stu-id="dcbd9-110">Packages</span></span>
* <span data-ttu-id="dcbd9-111">Eine Runtime (nur [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd))</span><span class="sxs-lookup"><span data-stu-id="dcbd9-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="dcbd9-112">App-Typ</span><span class="sxs-lookup"><span data-stu-id="dcbd9-112">App Type</span></span> | <span data-ttu-id="dcbd9-113">Verzeichnisstruktur</span><span class="sxs-lookup"><span data-stu-id="dcbd9-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="dcbd9-114">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="dcbd9-114">Framework-dependent Deployment</span></span>](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li><span data-ttu-id="dcbd9-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="dcbd9-115">publish&dagger;</span></span><ul><li><span data-ttu-id="dcbd9-116">Ansichten&dagger; (MVC-Apps, wenn Ansichten nicht vorkompiliert sind)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-116">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="dcbd9-117">Seiten&dagger; (MVC- oder Razor Pages-Apps, wenn Seiten nicht vorkompiliert sind)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-117">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="dcbd9-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="dcbd9-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="dcbd9-119">\*\.DLL-Dateien</span><span class="sxs-lookup"><span data-stu-id="dcbd9-119">\*\.dll files</span></span></li><li><span data-ttu-id="dcbd9-120">{ASSEMBLYNAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="dcbd9-120">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="dcbd9-121">{ASSEMBLYNAME}.dll</span><span class="sxs-lookup"><span data-stu-id="dcbd9-121">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="dcbd9-122">{ASSEMBLYNAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="dcbd9-122">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="dcbd9-123">{ASSEMBLYNAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="dcbd9-123">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="dcbd9-124">{ASSEMBLYNAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="dcbd9-124">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="dcbd9-125">{ASSEMBLYNAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="dcbd9-125">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="dcbd9-126">web.config (IIS-Bereitstellungen)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-126">web.config (IIS deployments)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="dcbd9-127">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="dcbd9-127">Self-contained Deployment</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="dcbd9-128">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="dcbd9-128">publish&dagger;</span></span><ul><li><span data-ttu-id="dcbd9-129">Ansichten&dagger; (MVC-Apps, wenn Ansichten nicht vorkompiliert sind)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-129">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="dcbd9-130">Seiten&dagger; (MVC- oder Razor Pages-Apps, wenn Seiten nicht vorkompiliert sind)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-130">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="dcbd9-131">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="dcbd9-131">wwwroot&dagger;</span></span></li><li><span data-ttu-id="dcbd9-132">\*DLL-Dateien</span><span class="sxs-lookup"><span data-stu-id="dcbd9-132">\*.dll files</span></span></li><li><span data-ttu-id="dcbd9-133">{ASSEMBLYNAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="dcbd9-133">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="dcbd9-134">{ASSEMBLYNAME}.dll</span><span class="sxs-lookup"><span data-stu-id="dcbd9-134">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="dcbd9-135">{ASSEMBLYNAME}.exe</span><span class="sxs-lookup"><span data-stu-id="dcbd9-135">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="dcbd9-136">{ASSEMBLYNAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="dcbd9-136">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="dcbd9-137">{ASSEMBLYNAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="dcbd9-137">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="dcbd9-138">{ASSEMBLYNAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="dcbd9-138">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="dcbd9-139">{ASSEMBLYNAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="dcbd9-139">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="dcbd9-140">web.config (IIS-Bereitstellungen)</span><span class="sxs-lookup"><span data-stu-id="dcbd9-140">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="dcbd9-141">&dagger;Gibt ein Verzeichnis an</span><span class="sxs-lookup"><span data-stu-id="dcbd9-141">&dagger;Indicates a directory</span></span>

<span data-ttu-id="dcbd9-142">Das Verzeichnis *publish* stellt den *Pfad des Inhaltsstammverzeichnisses* (auch als *Pfad der Anwendungsbasis* bekannt) der Bereitstellung dar.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-142">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="dcbd9-143">Unabhängig vom Namen des Verzeichnisses *publish* der auf dem Server bereitgestellten App dient der Speicherort des Verzeichnisses als physischer Pfad des Servers für die gehostete App.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-143">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="dcbd9-144">Das Verzeichnis *wwwroot* enthält, sofern vorhanden, nur statische Objekte.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-144">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dcbd9-145">Das Erstellen eines *Logs*-Ordners ist für die [erweiterte Debugprotokollierung für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) nützlich.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-145">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="dcbd9-146">Ordner im Pfad, die für den `<handlerSetting>`-Wert bereitgestellt werden, werden nicht automatisch vom Modul erstellt und müssen zuvor in der Bereitstellung vorhanden sein, damit das Modul das Debugprotokoll schreiben kann.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-146">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="dcbd9-147">Das Verzeichnis *Logs* (Protokolle) kann mit einem der folgenden beiden Ansätze für die Bereitstellung erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="dcbd9-147">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="dcbd9-148">Fügen Sie das folgende `<Target>`-Element zur Projektdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="dcbd9-148">Add the following `<Target>` element to the project file:</span></span>

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

   <span data-ttu-id="dcbd9-149">Mit dem `<MakeDir>`-Element wird in der veröffentlichten Ausgabe ein leerer *Logs*-Ordner erstellt.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-149">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="dcbd9-150">Das Element bestimmt mithilfe der Eigenschaft `PublishDir` den Zielspeicherort für die Erstellung des Ordners.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-150">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="dcbd9-151">Mehrere Bereitstellungsmethoden, wie z.B. Web Deploy, überspringen leere Ordner während der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-151">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="dcbd9-152">Das Element `<WriteLinesToFile>` generiert im Ordner *Logs* eine Datei, die eine Bereitstellung des Ordners auf dem Server garantiert.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-152">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="dcbd9-153">Eine Ordnererstellung mit dieser Vorgehensweise schlägt fehl, wenn der Workerprozess keinen Schreibzugriff auf den Zielordner hat.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-153">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="dcbd9-154">Erstellen Sie das Verzeichnis *Logs* physisch auf dem Server in der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-154">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="dcbd9-155">Für das Bereitstellungsverzeichnis sind Lese-/Ausführungsberechtigungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-155">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="dcbd9-156">Für das Verzeichnis *Logs* sind Lese-/Schreibberechtigungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-156">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="dcbd9-157">Für weitere Verzeichnisse, in die Dateien geschrieben werden, sind Lese-/Schreibberechtigungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="dcbd9-157">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="dcbd9-158">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="dcbd9-158">Additional resources</span></span>

* [<span data-ttu-id="dcbd9-159">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="dcbd9-159">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="dcbd9-160">.NET Core-Anwendungsbereitstellung</span><span class="sxs-lookup"><span data-stu-id="dcbd9-160">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="dcbd9-161">Zielframeworks</span><span class="sxs-lookup"><span data-stu-id="dcbd9-161">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="dcbd9-162">.NET Core-RID-Katalog</span><span class="sxs-lookup"><span data-stu-id="dcbd9-162">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
