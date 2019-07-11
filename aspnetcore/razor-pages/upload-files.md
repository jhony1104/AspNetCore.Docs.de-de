---
title: Hochladen von Dateien auf eine Razor-Seite in ASP.NET Core
author: guardrex
description: Informationen Sie zum Hochladen von Dateien zu einer Razor Page in ASP.NET Core mithilfe der FileUpload-Klasse.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: 14d10424951e8ec3c7909d001c6f86e5fcb45d26
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815045"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="113a3-103">Hochladen von Dateien auf eine Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="113a3-103">Upload files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="113a3-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="113a3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="113a3-105">Dieses Thema baut auf den [Beispiel-app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) in <xref:tutorials/razor-pages/razor-pages-start>.</span><span class="sxs-lookup"><span data-stu-id="113a3-105">This topic builds upon the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) in <xref:tutorials/razor-pages/razor-pages-start>.</span></span>

<span data-ttu-id="113a3-106">In diesem Thema zeigt, wie einfache modellbindung zum Hochladen von Dateien, verwendet die eignet sich gut für kleine Dateien hochladen.</span><span class="sxs-lookup"><span data-stu-id="113a3-106">This topic shows how to use simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="113a3-107">Informationen zum Streamen von großen Dateien finden Sie unter [Hochladen großer Dateien über Streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span><span class="sxs-lookup"><span data-stu-id="113a3-107">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="113a3-108">In den folgenden Schritten fügen Sie der Beispiel-App eine Funktion zum Hochladen eines Filmzeitplans hinzu.</span><span class="sxs-lookup"><span data-stu-id="113a3-108">In the following steps, a movie schedule file upload feature is added to the sample app.</span></span> <span data-ttu-id="113a3-109">Ein Filmzeitplan wird durch eine `Schedule`-Klasse dargestellt.</span><span class="sxs-lookup"><span data-stu-id="113a3-109">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="113a3-110">Die Klasse enthält zwei Versionen des Zeitplans.</span><span class="sxs-lookup"><span data-stu-id="113a3-110">The class includes two versions of the schedule.</span></span> <span data-ttu-id="113a3-111">Eine Version wird für Kunden bereitgestellt, `PublicSchedule`.</span><span class="sxs-lookup"><span data-stu-id="113a3-111">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="113a3-112">Die andere Version wird für Mitarbeiter des Unternehmens verwendet, `PrivateSchedule`.</span><span class="sxs-lookup"><span data-stu-id="113a3-112">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="113a3-113">Jede Version wird als separate Datei hochgeladen.</span><span class="sxs-lookup"><span data-stu-id="113a3-113">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="113a3-114">Das Tutorial veranschaulicht, wie zwei Dateiuploads von einer Seite mit einem einzigen POST an den Server durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="113a3-114">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

<span data-ttu-id="113a3-115">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="113a3-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="113a3-116">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="113a3-116">Security considerations</span></span>

<span data-ttu-id="113a3-117">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="113a3-117">Caution must be taken when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="113a3-118">Angreifer könnten [Denial-of-Service-Angriffe](/windows-hardware/drivers/ifs/denial-of-service) und andere Angriffe auf das System ausführen.</span><span class="sxs-lookup"><span data-stu-id="113a3-118">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) and other attacks on a system.</span></span> <span data-ttu-id="113a3-119">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="113a3-119">Some security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="113a3-120">Laden Sie Dateien in einem dedizierten Uploadbereich auf dem System hoch, wodurch es einfacher ist, Sicherheitsmaßnahmen für den hochgeladenen Inhalt zu ergreifen.</span><span class="sxs-lookup"><span data-stu-id="113a3-120">Upload files to a dedicated file upload area on the system, which makes it easier to impose security measures on uploaded content.</span></span> <span data-ttu-id="113a3-121">Stellen Sie beim Erteilen der Berechtigung zum Hochladen von Dateien sicher, dass für den Uploadbereich keine Ausführungsberechtigungen gewährt wurden.</span><span class="sxs-lookup"><span data-stu-id="113a3-121">When permitting file uploads, make sure that execute permissions are disabled on the upload location.</span></span>
* <span data-ttu-id="113a3-122">Verwenden Sie einen von der App bestimmten sicheren Dateinamen, keine vom Benutzer eingegebenen Namen oder den Dateinamen der hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="113a3-122">Use a safe file name determined by the app, not from user input or the file name of the uploaded file.</span></span>
* <span data-ttu-id="113a3-123">Lassen Sie nur einen festgelegten Satz genehmigter Dateierweiterungen zu.</span><span class="sxs-lookup"><span data-stu-id="113a3-123">Only allow a specific set of approved file extensions.</span></span>
* <span data-ttu-id="113a3-124">Stellen Sie sicher, dass clientseitige Überprüfungen auf dem Server durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="113a3-124">Verify client-side checks are performed on the server.</span></span> <span data-ttu-id="113a3-125">Clientseitige Überprüfungen sind leicht zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="113a3-125">Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="113a3-126">Überprüfen Sie die Größe des Uploads, und verhindern Sie Uploads von unerwartet großen Dateien.</span><span class="sxs-lookup"><span data-stu-id="113a3-126">Check the size of the upload and prevent larger uploads than expected.</span></span>
* <span data-ttu-id="113a3-127">Führen Sie für den hochgeladenen Inhalt eine Viren-/Malwareüberprüfung durch.</span><span class="sxs-lookup"><span data-stu-id="113a3-127">Run a virus/malware scanner on uploaded content.</span></span>

> [!WARNING]
> <span data-ttu-id="113a3-128">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="113a3-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
> * <span data-ttu-id="113a3-129">Vollständige Übernahme eines Systems</span><span class="sxs-lookup"><span data-stu-id="113a3-129">Completely takeover a system.</span></span>
> * <span data-ttu-id="113a3-130">Überladen des Systems mit dem Ziel eines vollständigen Systemausfalls</span><span class="sxs-lookup"><span data-stu-id="113a3-130">Overload a system with the result that the system completely fails.</span></span>
> * <span data-ttu-id="113a3-131">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="113a3-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="113a3-132">Anwenden von Graffiti auf eine öffentliche Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="113a3-132">Apply graffiti to a public interface.</span></span>

## <a name="add-a-fileupload-class"></a><span data-ttu-id="113a3-133">Hinzufügen einer FileUpload-Klasse</span><span class="sxs-lookup"><span data-stu-id="113a3-133">Add a FileUpload class</span></span>

<span data-ttu-id="113a3-134">Erstellen Sie eine Razor Page, die einige Dateiuploads verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="113a3-134">Create a Razor Page to handle a pair of file uploads.</span></span> <span data-ttu-id="113a3-135">Fügen Sie eine `FileUpload`-Klasse hinzu, die an die Seite gebunden ist, um die Zeitplandaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="113a3-135">Add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="113a3-136">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="113a3-136">Right click the *Models* folder.</span></span> <span data-ttu-id="113a3-137">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="113a3-137">Select **Add** > **Class**.</span></span> <span data-ttu-id="113a3-138">Nennen Sie die Klasse **FileUpload**, und fügen Sie Ihr die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-138">Name the class **FileUpload** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

<span data-ttu-id="113a3-139">Die Klasse verfügt über eine Eigenschaft für den Titel des Zeitplans und eine Eigenschaft für jede der beiden Versionen des Zeitplans.</span><span class="sxs-lookup"><span data-stu-id="113a3-139">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="113a3-140">Alle drei Eigenschaften sind erforderlich, und der Titel muss 3 bis 60 Zeichen lang sein.</span><span class="sxs-lookup"><span data-stu-id="113a3-140">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="113a3-141">Hinzufügen einer Hilfsmethode zum Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="113a3-141">Add a helper method to upload files</span></span>

<span data-ttu-id="113a3-142">Um Codeduplikate für die Verarbeitung hochgeladener Zeitplandateien zu vermeiden, fügen Sie zunächst eine statische Hilfsmethode hinzu.</span><span class="sxs-lookup"><span data-stu-id="113a3-142">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="113a3-143">Erstellen Sie einen *Dienstprogramme*-Ordner in der App, und fügen Sie eine *FileHelpers.cs*-Datei mit dem folgenden Inhalt hinzu.</span><span class="sxs-lookup"><span data-stu-id="113a3-143">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="113a3-144">Die Hilfsmethode `ProcessFormFile` verwendet [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) und [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary), und gibt eine Zeichenfolge zurück, in der die Größe und der Inhalt der Datei enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="113a3-144">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="113a3-145">Der Inhaltstyp und die Länge werden überprüft.</span><span class="sxs-lookup"><span data-stu-id="113a3-145">The content type and length are checked.</span></span> <span data-ttu-id="113a3-146">Wenn die Datei die Validierungsüberprüfung nicht besteht, wird ein Fehler zu `ModelState` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="113a3-146">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a><span data-ttu-id="113a3-147">Speichern der Datei auf einem Datenträger</span><span class="sxs-lookup"><span data-stu-id="113a3-147">Save the file to disk</span></span>

<span data-ttu-id="113a3-148">Die Beispiel-App speichert hochgeladene Dateien in Datenbankfeldern.</span><span class="sxs-lookup"><span data-stu-id="113a3-148">The sample app saves uploaded files into database fields.</span></span> <span data-ttu-id="113a3-149">Verwenden Sie ein [FileStream](/dotnet/api/system.io.filestream)-Objekt, um eine Datei auf dem Datenträger zu speichern.</span><span class="sxs-lookup"><span data-stu-id="113a3-149">To save a file to disk, use a [FileStream](/dotnet/api/system.io.filestream).</span></span> <span data-ttu-id="113a3-150">Im folgenden Beispiel wird eine Datei, die sich in `FileUpload.UploadPublicSchedule` befindet, in ein `FileStream`-Objekt innerhalb der `OnPostAsync`-Methode kopiert.</span><span class="sxs-lookup"><span data-stu-id="113a3-150">The following example copies a file held by `FileUpload.UploadPublicSchedule` to a `FileStream` in an `OnPostAsync` method.</span></span> <span data-ttu-id="113a3-151">Das `FileStream`-Objekt schreibt die Datei unter dem angegebenen Pfad und Dateinamen (`<PATH-AND-FILE-NAME>`) auf den Datenträger:</span><span class="sxs-lookup"><span data-stu-id="113a3-151">The `FileStream` writes the file to disk at the `<PATH-AND-FILE-NAME>` provided:</span></span>

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

<span data-ttu-id="113a3-152">Der Workerprozess muss über Schreibberechtigungen für den durch `filePath` angegebenen Speicherort verfügen.</span><span class="sxs-lookup"><span data-stu-id="113a3-152">The worker process must have write permissions to the location specified by `filePath`.</span></span>

> [!NOTE]
> <span data-ttu-id="113a3-153">Das `filePath`-Objekt *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="113a3-153">The `filePath` *must* include the file name.</span></span> <span data-ttu-id="113a3-154">Wird der Dateiname nicht angegeben, wird eine [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) zur Laufzeit ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="113a3-154">If the file name isn't provided, an [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) is thrown at runtime.</span></span>

> [!WARNING]
> <span data-ttu-id="113a3-155">Hochgeladene Dateien dürfen nie persistent in der Verzeichnisstruktur gespeichert werden, in der sich auch die App befindet.</span><span class="sxs-lookup"><span data-stu-id="113a3-155">Never persist uploaded files in the same directory tree as the app.</span></span>
>
> <span data-ttu-id="113a3-156">Im Codebeispiel wird kein serverseitiger Schutz vor Uploads von Dateien mit Schadsoftware bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="113a3-156">The code sample provides no server-side protection against malicious file uploads.</span></span> <span data-ttu-id="113a3-157">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="113a3-157">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="113a3-158">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="113a3-158">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="113a3-159">Azure-Sicherheit: Stellen Sie sicher, dass entsprechende Kontrolle gelten, wenn Dateien vom Benutzer akzeptiert</span><span class="sxs-lookup"><span data-stu-id="113a3-159">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a><span data-ttu-id="113a3-160">Speichern der Datei in Azure Blob Storage</span><span class="sxs-lookup"><span data-stu-id="113a3-160">Save the file to Azure Blob Storage</span></span>

<span data-ttu-id="113a3-161">Informationen zum Speichern von Dateiinhalt in Azure Blob Storage finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span><span class="sxs-lookup"><span data-stu-id="113a3-161">To upload file content to Azure Blob Storage, see [Get started with Azure Blob Storage using .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span></span> <span data-ttu-id="113a3-162">Das Thema veranschaulicht, wie Sie mit [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) einen [FileStream](/dotnet/api/system.io.filestream) in Blob Storage speichern.</span><span class="sxs-lookup"><span data-stu-id="113a3-162">The topic demonstrates how to use [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) to save a [FileStream](/dotnet/api/system.io.filestream) to blob storage.</span></span>

## <a name="add-the-schedule-class"></a><span data-ttu-id="113a3-163">Hinzufügen der Schedule-Klasse</span><span class="sxs-lookup"><span data-stu-id="113a3-163">Add the Schedule class</span></span>

<span data-ttu-id="113a3-164">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="113a3-164">Right click the *Models* folder.</span></span> <span data-ttu-id="113a3-165">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="113a3-165">Select **Add** > **Class**.</span></span> <span data-ttu-id="113a3-166">Nennen Sie die Klasse **Schedule**, und fügen Sie Ihr die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-166">Name the class **Schedule** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

<span data-ttu-id="113a3-167">Die Klasse verwendet `Display`- und `DisplayFormat`-Attribute, die benutzerfreundliche Titel und Formatierungen erzeugen, wenn die Zeitplandaten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="113a3-167">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a><span data-ttu-id="113a3-168">Aktualisieren von RazorPagesMovieContext</span><span class="sxs-lookup"><span data-stu-id="113a3-168">Update the RazorPagesMovieContext</span></span>

<span data-ttu-id="113a3-169">Geben Sie eine `DbSet`-Klasse in der `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) für die Zeitpläne ein:</span><span class="sxs-lookup"><span data-stu-id="113a3-169">Specify a `DbSet` in the `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a><span data-ttu-id="113a3-170">Aktualisieren von MovieContext</span><span class="sxs-lookup"><span data-stu-id="113a3-170">Update the MovieContext</span></span>

<span data-ttu-id="113a3-171">Geben Sie eine `DbSet`-Klasse in der `MovieContext` (*Models/MovieContext.cs*) für die Zeitpläne ein:</span><span class="sxs-lookup"><span data-stu-id="113a3-171">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="113a3-172">Hinzufügen der Zeitplantabelle zur Datenbank</span><span class="sxs-lookup"><span data-stu-id="113a3-172">Add the Schedule table to the database</span></span>

<span data-ttu-id="113a3-173">Öffnen Sie die Paket-Manager-Konsole (PMC): **Tools** > **NuGet-Paket-Manager** >  **-Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="113a3-173">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![PMC-Menü](upload-files/_static/pmc.png)

<span data-ttu-id="113a3-175">Führen Sie in der PMC die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="113a3-175">In the PMC, execute the following commands.</span></span> <span data-ttu-id="113a3-176">Diese Befehle fügen eine `Schedule`-Tabelle zur Datenbank hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-176">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="113a3-177">Hinzufügen einer Dateiupload-Razor-Seite</span><span class="sxs-lookup"><span data-stu-id="113a3-177">Add a file upload Razor Page</span></span>

<span data-ttu-id="113a3-178">Erstellen Sie im Ordner *Seiten* einen Ordner *Zeitpläne*.</span><span class="sxs-lookup"><span data-stu-id="113a3-178">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="113a3-179">Erstellen Sie im Ordner *Zeitpläne* eine Seite namens *Index.cshtml* zum Hochladen eines Zeitplans mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="113a3-179">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

<span data-ttu-id="113a3-180">Jede Formulargruppe enthält ein **\<label>** , das den Namen jeder Klasseneigenschaft anzeigt.</span><span class="sxs-lookup"><span data-stu-id="113a3-180">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="113a3-181">Die `Display`-Attribute im `FileUpload`-Modell stellen die Anzeigewerte für die Bezeichnungen bereit.</span><span class="sxs-lookup"><span data-stu-id="113a3-181">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="113a3-182">Beispielsweise ist als Anzeigename für die `UploadPublicSchedule`-Eigenschaft `[Display(Name="Public Schedule")]` festgelegt, wodurch „Public Schedule“ in der Bezeichnung angezeigt wird, wenn das Formular rendert.</span><span class="sxs-lookup"><span data-stu-id="113a3-182">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="113a3-183">Jede Formulargruppe umfasst einen Validierungsbereich ( **\<span>** ).</span><span class="sxs-lookup"><span data-stu-id="113a3-183">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="113a3-184">Wenn die Eingabe des Benutzers nicht mit den Eigenschaftsattributen übereinstimmt, die in der `FileUpload`-Klasse festgelegt wurden, oder wenn eine der Dateivalidierungsüberprüfungen der `ProcessFormFile`-Methode fehlschlägt, schlägt die Validierung des Modells fehl.</span><span class="sxs-lookup"><span data-stu-id="113a3-184">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="113a3-185">Wenn die Modellvalidierung fehlschlägt, wird eine hilfreiche Validierungsmeldung für den Benutzer gerendert.</span><span class="sxs-lookup"><span data-stu-id="113a3-185">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="113a3-186">Beispielsweise erhält die `Title`-Eigenschaft die Anmerkungen `[Required]` und `[StringLength(60, MinimumLength = 3)]`.</span><span class="sxs-lookup"><span data-stu-id="113a3-186">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="113a3-187">Wenn der Benutzer keinen Titel angibt, erhält er eine Meldung, die angibt, dass ein Wert erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="113a3-187">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="113a3-188">Wenn ein Benutzer einen Wert eingibt, der weniger als 3 oder mehr als 60 Zeichen umfasst, erhält er eine Meldung, die angibt, dass die Länge des Werts falsch ist.</span><span class="sxs-lookup"><span data-stu-id="113a3-188">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="113a3-189">Wenn eine Datei ohne Inhalt bereitgestellt wird, wird eine Meldung angezeigt, dass die Datei leer ist.</span><span class="sxs-lookup"><span data-stu-id="113a3-189">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-page-model"></a><span data-ttu-id="113a3-190">Hinzufügen des Seitenmodells</span><span class="sxs-lookup"><span data-stu-id="113a3-190">Add the page model</span></span>

<span data-ttu-id="113a3-191">Fügen Sie das Seitenmodell (*Index.cshtml.cs*) zu dem Ordner *Zeitpläne* hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-191">Add the page model (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

<span data-ttu-id="113a3-192">Das Seitenmodell (`IndexModel` in *Index.cshtml.cs*) bindet die `FileUpload`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="113a3-192">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="113a3-193">Das Modell verwendet zudem eine Liste der Zeitpläne (`IList<Schedule>`), um die in der Datenbank gespeicherten Zeitpläne auf der Seite anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="113a3-193">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="113a3-194">Wenn die Seite mit `OnGetAsync` geladen wird, wird `Schedules` aus der Datenbank aufgefüllt und dazu verwendet, eine HTML-Tabelle geladener Zeitpläne zu generieren:</span><span class="sxs-lookup"><span data-stu-id="113a3-194">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="113a3-195">Wenn das Formular auf dem Server bereitgestellt wird, wird `ModelState` überprüft.</span><span class="sxs-lookup"><span data-stu-id="113a3-195">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="113a3-196">Falls ungültig, wird `Schedule` erneut erstellt, und die Seite rendert mit mindestens einer Validierungsmeldung, die angibt, warum die Validierung der Seite fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="113a3-196">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="113a3-197">Falls gültig, werden die `FileUpload`-Eigenschaften in *OnPostAsync* verwendet, um den Dateiupload für die beiden Versionen des Zeitplans abzuschließen und um ein neues `Schedule`-Objekt zu erstellen, um die Daten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="113a3-197">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="113a3-198">Der Zeitplan wird dann in der Datenbank gespeichert:</span><span class="sxs-lookup"><span data-stu-id="113a3-198">The schedule is then saved to the database:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="113a3-199">Verknüpfen der Dateiupload-Razor-Seite</span><span class="sxs-lookup"><span data-stu-id="113a3-199">Link the file upload Razor Page</span></span>

<span data-ttu-id="113a3-200">Öffnen Sie *Pages/Shared/_Layout.cshtml*, und fügen Sie einen Link zur Navigationsleiste hinzu, um die Seite mit den Zeitplänen zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="113a3-200">Open *Pages/Shared/_Layout.cshtml* and add a link to the navigation bar to reach the Schedules page:</span></span>

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="113a3-201">Hinzufügen einer Seite zum Bestätigen des Zeitplan-Löschvorgangs</span><span class="sxs-lookup"><span data-stu-id="113a3-201">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="113a3-202">Wenn der Benutzer klickt, um einen Zeitplan zu löschen, erhält er die Möglichkeit zum Abbrechen des Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="113a3-202">When the user clicks to delete a schedule, a chance to cancel the operation is provided.</span></span> <span data-ttu-id="113a3-203">Fügen Sie eine Seite zum Bestätigen des Löschvorgangs (*Delete.cshtml*) zum *Zeitpläne*-Ordner hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-203">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

<span data-ttu-id="113a3-204">Im Seitenmodell (*Delete.cshtml.cs*) lädt einen einzelnen Zeitplan, der durch `id` in den Routendaten der Anforderung identifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="113a3-204">The page model (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="113a3-205">Fügen Sie die Datei *Delete.cshtml.cs* zum *Zeitpläne*-Ordner hinzu:</span><span class="sxs-lookup"><span data-stu-id="113a3-205">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

<span data-ttu-id="113a3-206">Die `OnPostAsync`-Methode verarbeitet das Löschen des Zeitplans über ihre `id`:</span><span class="sxs-lookup"><span data-stu-id="113a3-206">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

<span data-ttu-id="113a3-207">Nachdem der Zeitplan erfolgreich gelöscht wurde, sendet `RedirectToPage` den Benutzer zurück zur *Index.cshtml*-Seite der Zeitpläne.</span><span class="sxs-lookup"><span data-stu-id="113a3-207">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="113a3-208">Die Razor Page der Arbeitszeitpläne</span><span class="sxs-lookup"><span data-stu-id="113a3-208">The working Schedules Razor Page</span></span>

<span data-ttu-id="113a3-209">Wenn die Seite geladen wird, werden Bezeichnungen und Eingaben für den Zeitplantitel, den öffentlichen Zeitplan und den privaten Zeitplan mit einer „Absenden“-Schaltfläche gerendert:</span><span class="sxs-lookup"><span data-stu-id="113a3-209">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![Razor Page der Zeitpläne wie beim ersten Ladevorgang ohne Validierungsfehler und leere Felder](upload-files/_static/browser1.png)

<span data-ttu-id="113a3-211">Das Klicken auf die Schaltfläche **Hochladen**, ohne eines der Felder aufzufüllen, verstößt gegen die `[Required]`-Attribute für das Modell.</span><span class="sxs-lookup"><span data-stu-id="113a3-211">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="113a3-212">`ModelState` ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="113a3-212">The `ModelState` is invalid.</span></span> <span data-ttu-id="113a3-213">Die Validierungsfehlermeldungen werden dem Benutzer angezeigt:</span><span class="sxs-lookup"><span data-stu-id="113a3-213">The validation error messages are displayed to the user:</span></span>

![Die Validierungsfehlermeldungen werden neben jedem Eingabesteuerelement angezeigt](upload-files/_static/browser2.png)

<span data-ttu-id="113a3-215">Geben Sie zwei Buchstaben in das Feld **Titel** ein.</span><span class="sxs-lookup"><span data-stu-id="113a3-215">Type two letters into the **Title** field.</span></span> <span data-ttu-id="113a3-216">Die Validierungsmeldung ändert sich und gibt an, dass der Titel zwischen 3 und 60 Zeichen aufweisen muss:</span><span class="sxs-lookup"><span data-stu-id="113a3-216">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![Validierungsmeldung zum Titel wurde geändert](upload-files/_static/browser3.png)

<span data-ttu-id="113a3-218">Wenn ein oder mehrere Zeitpläne hochgeladen werden, rendert der Bereich **Loaded Schedules** (Geladene Zeitpläne) die geladenen Zeitpläne:</span><span class="sxs-lookup"><span data-stu-id="113a3-218">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![Tabelle der geladenen Zeitpläne, die den Titel jedes Zeitplans, den Uploadzeitpunkt in UTC, die Dateigröße der öffentlichen Version und die Dateigröße der privaten Version anzeigt](upload-files/_static/browser4.png)

<span data-ttu-id="113a3-220">Der Benutzer kann von dort aus auf den Link **Löschen** klicken, um zur Anzeige der Bestätigung des Löschvorgangs zu gelangen, die die Möglichkeit bietet, diesen zu bestätigen oder abzubrechen.</span><span class="sxs-lookup"><span data-stu-id="113a3-220">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="113a3-221">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="113a3-221">Troubleshooting</span></span>

<span data-ttu-id="113a3-222">Für die Problembehandlung von Informationen mit `IFormFile` hochladen, finden Sie [Dateiuploads in ASP.NET Core: Problembehandlung bei](xref:mvc/models/file-uploads#troubleshooting).</span><span class="sxs-lookup"><span data-stu-id="113a3-222">For troubleshooting information with `IFormFile` uploading, see [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>
