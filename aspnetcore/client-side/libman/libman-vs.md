---
title: LibMan mit ASP.NET Core in Visual Studio verwenden
author: scottaddie
description: Erfahren Sie, wie LibMan in einem ASP.NET Core-Projekt mit Visual Studio verwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: 41a5a41c8921b04290784d26441ecb46aea753e7
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894967"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="cd95f-103">LibMan mit ASP.NET Core in Visual Studio verwenden</span><span class="sxs-lookup"><span data-stu-id="cd95f-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="cd95f-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cd95f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cd95f-105">Visual Studio verfügt über integrierte Unterstützung für [LibMan](xref:client-side/libman/index) in ASP.NET Core-Projekte, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="cd95f-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="cd95f-106">Unterstützung zum Konfigurieren und Ausführen von Wiederherstellungsvorgängen LibMan, bei der Erstellung.</span><span class="sxs-lookup"><span data-stu-id="cd95f-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="cd95f-107">Menüelemente für das Auslösen eines LibMan wiederherstellen und Bereinigen von Vorgängen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="cd95f-108">Das Suchdialogfeld zum Suchen von Bibliotheken, und die Dateien zu einem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="cd95f-109">Bearbeiten Sie die Unterstützung für *libman.json*&mdash;LibMan Manifestdatei.</span><span class="sxs-lookup"><span data-stu-id="cd95f-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="cd95f-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(Herunterladen von)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cd95f-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cd95f-111">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="cd95f-111">Prerequisites</span></span>

* <span data-ttu-id="cd95f-112">Visual Studio 2017 Version 15,8 oder höher mit der **ASP.NET und Webentwicklung** arbeitsauslastung</span><span class="sxs-lookup"><span data-stu-id="cd95f-112">Visual Studio 2017 version 15.8 or later with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="cd95f-113">Fügen Sie Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="cd95f-113">Add library files</span></span>

<span data-ttu-id="cd95f-114">Bibliotheksdateien können auf zwei unterschiedliche Arten zu einem ASP.NET Core-Projekt hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cd95f-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="cd95f-115">Verwenden Sie das Dialogfeld "Client-Side-Bibliothek hinzufügen"</span><span class="sxs-lookup"><span data-stu-id="cd95f-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="cd95f-116">LibMan Manifestdatei Einträge manuell konfigurieren</span><span class="sxs-lookup"><span data-stu-id="cd95f-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="cd95f-117">Verwenden Sie das Dialogfeld "Client-Side-Bibliothek hinzufügen"</span><span class="sxs-lookup"><span data-stu-id="cd95f-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="cd95f-118">Um eine clientseitige Bibliothek zu installieren, gehen Sie wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="cd95f-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="cd95f-119">In **Projektmappen-Explorer**, mit der rechten Maustaste in des Projektordner aus, in dem die Dateien hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="cd95f-120">Wählen Sie **hinzufügen** > **eine clientseitige Bibliothek**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="cd95f-121">Die **hinzufügen eine clientseitige Bibliothek** Dialogfeld wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cd95f-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Eine clientseitige Bibliothek-Dialogfeld "hinzufügen"](_static/add-library-dialog.png)

* <span data-ttu-id="cd95f-123">Wählen Sie den Bibliotheksanbieter aus der **Anbieter** Dropdown-Liste.</span><span class="sxs-lookup"><span data-stu-id="cd95f-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="cd95f-124">CDNJS ist der Standardanbieter.</span><span class="sxs-lookup"><span data-stu-id="cd95f-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="cd95f-125">Geben Sie den Namen der Bibliothek in abzurufenden der **Bibliothek** Textfeld.</span><span class="sxs-lookup"><span data-stu-id="cd95f-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="cd95f-126">IntelliSense bietet eine Liste der Bibliotheken, die mit dem bereitgestellten Text ab.</span><span class="sxs-lookup"><span data-stu-id="cd95f-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="cd95f-127">Wählen Sie die Bibliothek aus der IntelliSense-Liste ein.</span><span class="sxs-lookup"><span data-stu-id="cd95f-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="cd95f-128">Beachten Sie, dass der Bibliotheksname ist mit dem Suffix der `@` Symbol und die neueste stabile Version für den ausgewählten Anbieter bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cd95f-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="cd95f-129">Entscheiden Sie, welche Dateien enthalten:</span><span class="sxs-lookup"><span data-stu-id="cd95f-129">Decide which files to include:</span></span>
  * <span data-ttu-id="cd95f-130">Wählen Sie die **enthalten alle Bibliotheksdateien** Optionsfeld, um alle Dateien mit der Bibliothek enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="cd95f-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="cd95f-131">Wählen Sie die **wählen Sie bestimmte Dateien** Optionsfeld, um eine Teilmenge der Bibliothek-Dateien enthalten.</span><span class="sxs-lookup"><span data-stu-id="cd95f-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="cd95f-132">Wenn das Optionsfeld ausgewählt ist, wird die Selector-Dateistruktur aktiviert.</span><span class="sxs-lookup"><span data-stu-id="cd95f-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="cd95f-133">Aktivieren Sie die Kontrollkästchen auf der linken Seite der Dateinamen zum Herunterladen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="cd95f-134">Geben Sie den Projektordner zum Speichern der Dateien in die **Zielspeicherort** Textfeld.</span><span class="sxs-lookup"><span data-stu-id="cd95f-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="cd95f-135">Als Empfehlung speichern Sie jede Bibliothek in einem separaten Ordner.</span><span class="sxs-lookup"><span data-stu-id="cd95f-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="cd95f-136">Die vorgeschlagenen **Zielspeicherort** Ordner basiert auf dem Standort, von dem aus das Dialogfeld gestartet:</span><span class="sxs-lookup"><span data-stu-id="cd95f-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="cd95f-137">Wenn Sie über das Stammverzeichnis des Projekts gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="cd95f-137">If launched from the project root:</span></span>
    * <span data-ttu-id="cd95f-138">*Wwwroot/Lib* wird verwendet, wenn *"Wwwroot"* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cd95f-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="cd95f-139">*Lib* wird verwendet, wenn *"Wwwroot"* ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cd95f-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="cd95f-140">Wenn aus einem Projektordner gestartet wird, wird der entsprechende Ordnername verwendet.</span><span class="sxs-lookup"><span data-stu-id="cd95f-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="cd95f-141">Der Ordner Vorschlag wird der Bibliotheksname angehängt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="cd95f-142">Die folgende Tabelle zeigt die Ordner-Vorschläge, bei der Installation von jQuery in einem Projekt mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cd95f-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="cd95f-143">Starten Sie Speicherort</span><span class="sxs-lookup"><span data-stu-id="cd95f-143">Launch location</span></span>                           |<span data-ttu-id="cd95f-144">Empfohlene Ordner</span><span class="sxs-lookup"><span data-stu-id="cd95f-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="cd95f-145">Stammverzeichnis des Projekts (Wenn *"Wwwroot"* vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="cd95f-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="cd95f-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="cd95f-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="cd95f-147">Stammverzeichnis des Projekts (Wenn *"Wwwroot"* ist nicht vorhanden.)</span><span class="sxs-lookup"><span data-stu-id="cd95f-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="cd95f-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="cd95f-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="cd95f-149">*Seiten* Ordner im Projekt</span><span class="sxs-lookup"><span data-stu-id="cd95f-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="cd95f-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="cd95f-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="cd95f-151">Klicken Sie auf die **installieren** Schaltfläche zum Herunterladen der Dateien, gemäß der Konfiguration in *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="cd95f-152">Überprüfen Sie die **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster für die Details zur Installation.</span><span class="sxs-lookup"><span data-stu-id="cd95f-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="cd95f-153">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd95f-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="cd95f-154">LibMan Manifestdatei Einträge manuell konfigurieren</span><span class="sxs-lookup"><span data-stu-id="cd95f-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="cd95f-155">Alle LibMan Vorgänge in Visual Studio basieren auf dem Inhalt des Manifests für das Stammverzeichnis des Projekts die LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="cd95f-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="cd95f-156">Sie können manuell bearbeiten *libman.json* Library-Dateien für das Projekt zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="cd95f-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="cd95f-157">Visual Studio stellt alle Bibliotheksdateien einmal wieder her. *libman.json* gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="cd95f-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="cd95f-158">Zum Öffnen *libman.json* für die Bearbeitung gibt folgende Optionen:</span><span class="sxs-lookup"><span data-stu-id="cd95f-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="cd95f-159">Doppelklicken Sie auf die *libman.json* Datei **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="cd95f-160">Mit der rechten Maustaste in des Projekts im **Projektmappen-Explorer** , und wählen Sie **clientseitige Bibliotheken verwalten**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="cd95f-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="cd95f-161">**&#8224;**</span></span>
* <span data-ttu-id="cd95f-162">Wählen Sie **clientseitige Bibliotheken verwalten** von Visual Studio **Projekt** Menü.</span><span class="sxs-lookup"><span data-stu-id="cd95f-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="cd95f-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="cd95f-163">**&#8224;**</span></span>

<span data-ttu-id="cd95f-164">**&#8224;** Wenn die *libman.json* Datei ist nicht in das Stammverzeichnis des Projekts bereits vorhanden ist, wird es mit den standardmäßigen Element Vorlage-Inhalt erstellt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="cd95f-165">Visual Studio bietet umfassende JSON bearbeitungsunterstützung wie z. B. farbliche Kennzeichnung, Formatierung, IntelliSense und Schema-Validierung.</span><span class="sxs-lookup"><span data-stu-id="cd95f-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="cd95f-166">Die JSON-Schema des Manifests LibMan befindet sich [ http://json.schemastore.org/libman ](http://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="cd95f-166">The LibMan manifest's JSON schema is found at [http://json.schemastore.org/libman](http://json.schemastore.org/libman).</span></span>

<span data-ttu-id="cd95f-167">Mit der folgenden Manifestdatei ruft LibMan Dateien gemäß der Konfiguration definiert, der `libraries` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="cd95f-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="cd95f-168">Eine Erläuterung der Objektliterale in definierten `libraries` folgt:</span><span class="sxs-lookup"><span data-stu-id="cd95f-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="cd95f-169">Eine Teilmenge der [jQuery](https://jquery.com/) Version 3.3.1 wird vom Anbieter CDNJS abgerufen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="cd95f-170">Die Teilmenge wird definiert, der `files` Eigenschaft&mdash;*"jQuery.Min.js"*, *"jQuery.js"*, und *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="cd95f-171">Die Dateien des Projekts platziert sind *Wwwroot/Lib/Jquery* Ordner.</span><span class="sxs-lookup"><span data-stu-id="cd95f-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="cd95f-172">Während des gesamten Entwicklungsprozesses [Bootstrap](https://getbootstrap.com/) Version 4.1.3 wird abgerufen und in einem *Wwwroot/Lib/Bootstrap* Ordner.</span><span class="sxs-lookup"><span data-stu-id="cd95f-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="cd95f-173">Das Objektliteral `provider` -Eigenschaft überschreibt den `defaultProvider` -Eigenschaftswert.</span><span class="sxs-lookup"><span data-stu-id="cd95f-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="cd95f-174">LibMan Ruft die Bootstrap-Dateien aus der Unpkg Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="cd95f-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="cd95f-175">Eine Teilmenge der [Lodash](https://lodash.com/) , die von einem Governanceorgane innerhalb der Organisation genehmigt wurde.</span><span class="sxs-lookup"><span data-stu-id="cd95f-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="cd95f-176">Die *lodash.js* und *lodash.min.js* Dateien werden abgerufen, aus dem lokalen Dateisystem auf *C:\\Temp\\Lodash\\*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="cd95f-177">Die Dateien des Projekts kopiert werden *Wwwroot/Lib/Lodash* Ordner.</span><span class="sxs-lookup"><span data-stu-id="cd95f-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="cd95f-178">LibMan unterstützt nur eine Version jeder Bibliothek aus jeder Anbieter.</span><span class="sxs-lookup"><span data-stu-id="cd95f-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="cd95f-179">Die *libman.json* Datei nicht Schema-Validierung, wenn sie die beiden Bibliotheken mit dem gleichen Bibliotheksnamen für einen angegebenen Anbieter enthält.</span><span class="sxs-lookup"><span data-stu-id="cd95f-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="cd95f-180">Wiederherstellen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="cd95f-180">Restore library files</span></span>

<span data-ttu-id="cd95f-181">Um Library-Dateien in Visual Studio wiederherzustellen, es muss ein gültiger *libman.json* -Datei in das Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="cd95f-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="cd95f-182">Wiederhergestellte Dateien werden in das Projekt, an dem für jede Bibliothek angegebenen Speicherort platziert.</span><span class="sxs-lookup"><span data-stu-id="cd95f-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="cd95f-183">Bibliotheksdateien können in einem ASP.NET Core-Projekt auf zweierlei Weise wiederhergestellt werden:</span><span class="sxs-lookup"><span data-stu-id="cd95f-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="cd95f-184">Wiederherstellen von Dateien während des Buildvorgangs</span><span class="sxs-lookup"><span data-stu-id="cd95f-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="cd95f-185">Wiederherstellen von Dateien manuell</span><span class="sxs-lookup"><span data-stu-id="cd95f-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="cd95f-186">Wiederherstellen von Dateien während des Buildvorgangs</span><span class="sxs-lookup"><span data-stu-id="cd95f-186">Restore files during build</span></span>

<span data-ttu-id="cd95f-187">LibMan kann die definierten-Bibliotheksdateien als Teil des Buildprozesses wiederherstellen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="cd95f-188">In der Standardeinstellung die *Wiederherstellung Builds* Verhalten deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="cd95f-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="cd95f-189">So aktivieren, und Testen Sie das Verhalten für die Wiederherstellung Builds:</span><span class="sxs-lookup"><span data-stu-id="cd95f-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="cd95f-190">Mit der rechten Maustaste *libman.json* in **Projektmappen-Explorer** , und wählen Sie **wiederherstellen clientseitige Bibliotheken auf Build aktivieren** aus dem Kontextmenü.</span><span class="sxs-lookup"><span data-stu-id="cd95f-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="cd95f-191">Klicken Sie auf die **Ja** Schaltfläche, wenn Sie aufgefordert werden, um ein NuGet-Paket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="cd95f-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="cd95f-192">Die [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet-Paket wird dem Projekt hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="cd95f-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="cd95f-193">Erstellen Sie das Projekt aus, um sicherzustellen, dass LibMan Datei Wiederherstellung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="cd95f-194">Die `Microsoft.Web.LibraryManager.Build` -Paket Fügt ein MSBuild-Ziel, die LibMan während der Buildvorgang des Projekts ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cd95f-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="cd95f-195">Überprüfen Sie die **erstellen** des Feeds der **Ausgabe** Fenster für ein Aktivitätsprotokoll LibMan:</span><span class="sxs-lookup"><span data-stu-id="cd95f-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="cd95f-196">Wenn das Verhalten für die Wiederherstellung Builds aktiviert ist, die *libman.json* Kontextmenü wird angezeigt. eine **deaktivieren wiederherstellen clientseitige Bibliotheken auf Build** Option.</span><span class="sxs-lookup"><span data-stu-id="cd95f-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="cd95f-197">Diese Option entfernt die `Microsoft.Web.LibraryManager.Build` Paketverweis aus der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="cd95f-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="cd95f-198">Die clientseitige Bibliotheken werden daher nicht mehr für jedes Build wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="cd95f-199">Unabhängig von der Restore-auf-Build-Einstellung, Sie können manuell wiederherstellen zu einem beliebigen Zeitpunkt aus der *libman.json* Kontextmenü.</span><span class="sxs-lookup"><span data-stu-id="cd95f-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="cd95f-200">Weitere Informationen finden Sie unter [Dateien manuell wiederherstellen](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="cd95f-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="cd95f-201">Wiederherstellen von Dateien manuell</span><span class="sxs-lookup"><span data-stu-id="cd95f-201">Restore files manually</span></span>

<span data-ttu-id="cd95f-202">Bibliotheksdateien manuell wiederherstellen zu können:</span><span class="sxs-lookup"><span data-stu-id="cd95f-202">To manually restore library files:</span></span>

* <span data-ttu-id="cd95f-203">Für alle Projekte in der Projektmappe:</span><span class="sxs-lookup"><span data-stu-id="cd95f-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="cd95f-204">Mit der rechten Maustaste in des Namens der Projektmappe **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="cd95f-205">Wählen Sie die **wiederherstellen clientseitige Bibliotheken** Option.</span><span class="sxs-lookup"><span data-stu-id="cd95f-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="cd95f-206">Für ein bestimmtes Projekt:</span><span class="sxs-lookup"><span data-stu-id="cd95f-206">For a specific project:</span></span>
  * <span data-ttu-id="cd95f-207">Mit der rechten Maustaste die *libman.json* Datei **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="cd95f-208">Wählen Sie die **wiederherstellen clientseitige Bibliotheken** Option.</span><span class="sxs-lookup"><span data-stu-id="cd95f-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="cd95f-209">Während des Wiederherstellungsvorgangs ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cd95f-209">While the restore operation is running:</span></span>

* <span data-ttu-id="cd95f-210">Das Symbol "Task Status Center (TSC)" in der Statusleiste von Visual Studio animiert wird, und liest *Wiederherstellungsvorgang Schritte*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="cd95f-211">Klicken auf das Symbol klicken, wird eine QuickInfo, die Auflistung der bekannten Hintergrundaufgaben geöffnet.</span><span class="sxs-lookup"><span data-stu-id="cd95f-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="cd95f-212">Nachrichten werden an die Statusleiste gesendet werden und die **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster.</span><span class="sxs-lookup"><span data-stu-id="cd95f-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="cd95f-213">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd95f-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="cd95f-214">Löschen Sie Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="cd95f-214">Delete library files</span></span>

<span data-ttu-id="cd95f-215">Zum Ausführen der *Bereinigen* Vorgang, bei dem Bibliotheksdateien, die zuvor in Visual Studio wiederhergestellt wird gelöscht:</span><span class="sxs-lookup"><span data-stu-id="cd95f-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="cd95f-216">Mit der rechten Maustaste die *libman.json* Datei **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="cd95f-217">Wählen Sie die **bereinigen clientseitige Bibliotheken** Option.</span><span class="sxs-lookup"><span data-stu-id="cd95f-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="cd95f-218">Um zu verhindern, dass unbeabsichtigt von nicht-Library-Dateien, nicht der Bereinigungsvorgang ganze Verzeichnisse zu löschen.</span><span class="sxs-lookup"><span data-stu-id="cd95f-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="cd95f-219">Es werden nur Dateien in die vorherige Wiederherstellung einbezogen wurden entfernt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="cd95f-220">Während die saubere Operation ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cd95f-220">While the clean operation is running:</span></span>

* <span data-ttu-id="cd95f-221">Das Symbol "TSC" auf der Statusleiste von Visual Studio animiert wird, und liest *Client-Bibliotheken Vorgang gestartet*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="cd95f-222">Klicken auf das Symbol klicken, wird eine QuickInfo, die Auflistung der bekannten Hintergrundaufgaben geöffnet.</span><span class="sxs-lookup"><span data-stu-id="cd95f-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="cd95f-223">Meldungen auf der Statusleiste und **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster.</span><span class="sxs-lookup"><span data-stu-id="cd95f-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="cd95f-224">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd95f-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="cd95f-225">Der Bereinigungsvorgang werden nur Dateien aus dem Projekt gelöscht.</span><span class="sxs-lookup"><span data-stu-id="cd95f-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="cd95f-226">Bibliotheksdateien bleiben im Cache für schnelleren Zugriff auf zukünftige Wiederherstellungsvorgänge.</span><span class="sxs-lookup"><span data-stu-id="cd95f-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="cd95f-227">Verwenden Sie zum Verwalten der Bibliotheksdateien, die aus dem Cache mit dem lokalen Computer die [LibMan CLI](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="cd95f-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="cd95f-228">Deinstallieren Sie Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="cd95f-228">Uninstall library files</span></span>

<span data-ttu-id="cd95f-229">So deinstallieren Sie Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="cd95f-229">To uninstall library files:</span></span>

* <span data-ttu-id="cd95f-230">Open *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-230">Open *libman.json*.</span></span>
* <span data-ttu-id="cd95f-231">Positionieren Sie den Textcursor in den entsprechenden `libraries` Objektliteral.</span><span class="sxs-lookup"><span data-stu-id="cd95f-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="cd95f-232">Klicken Sie auf das Glühbirnensymbol klicken, die in den linken Rand angezeigt wird, und wählen **Deinstallieren \<Library_name > @\<Library_version >**:</span><span class="sxs-lookup"><span data-stu-id="cd95f-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Deinstallieren Sie die Option im Kontextmenü für Bibliothek](_static/uninstall-menu-option.png)

<span data-ttu-id="cd95f-234">Sie können auch manuell bearbeiten und speichern Sie das Manifest LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="cd95f-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="cd95f-235">Die [Wiederherstellungsvorgang](#restore-library-files) ausgeführt wird, wenn die Datei gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="cd95f-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="cd95f-236">Bibliotheksdateien, die nicht mehr definiert sind *libman.json* aus dem Projekt entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="cd95f-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="cd95f-237">Update-Bibliotheksversion</span><span class="sxs-lookup"><span data-stu-id="cd95f-237">Update library version</span></span>

<span data-ttu-id="cd95f-238">So überprüfen Sie für eine Version für die aktualisierte Bibliothek</span><span class="sxs-lookup"><span data-stu-id="cd95f-238">To check for an updated library version:</span></span>

* <span data-ttu-id="cd95f-239">Open *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="cd95f-239">Open *libman.json*.</span></span>
* <span data-ttu-id="cd95f-240">Positionieren Sie den Textcursor in den entsprechenden `libraries` Objektliteral.</span><span class="sxs-lookup"><span data-stu-id="cd95f-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="cd95f-241">Klicken Sie auf das Glühbirnensymbol klicken, das in den linken Rand angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="cd95f-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="cd95f-242">Zeigen Sie auf **nach Updates suchen**.</span><span class="sxs-lookup"><span data-stu-id="cd95f-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="cd95f-243">LibMan überprüft, ob eine Bibliotheksversion ist neuer als die installierte Version.</span><span class="sxs-lookup"><span data-stu-id="cd95f-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="cd95f-244">Die folgenden Vorgänge möglich:</span><span class="sxs-lookup"><span data-stu-id="cd95f-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="cd95f-245">Ein **keine Updates gefunden** Meldung wird angezeigt, wenn die aktuelle Version bereits installiert ist.</span><span class="sxs-lookup"><span data-stu-id="cd95f-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="cd95f-246">Die neueste stabile Version wird angezeigt, sofern nicht bereits installiert.</span><span class="sxs-lookup"><span data-stu-id="cd95f-246">The latest stable version is displayed if not already installed.</span></span>

  ![Überprüfung auf Updates Kontextmenüoption "".](_static/update-menu-option.png)

* <span data-ttu-id="cd95f-248">Wenn eine Vorabversion, die neuer als die installierte Version verfügbar ist, wird die ältere Version angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="cd95f-249">Ein Downgrade auf eine ältere Bibliotheksversion manuell bearbeiten, die *libman.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="cd95f-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="cd95f-250">Wenn die Datei gespeichert wird, wird die LibMan [Wiederherstellungsvorgang](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="cd95f-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="cd95f-251">Entfernt redundante Dateien aus der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="cd95f-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="cd95f-252">Neue und aktualisierte Dateien werden von der neuen Version hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd95f-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd95f-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd95f-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="cd95f-254">GitHub-Repository für LibMan</span><span class="sxs-lookup"><span data-stu-id="cd95f-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
