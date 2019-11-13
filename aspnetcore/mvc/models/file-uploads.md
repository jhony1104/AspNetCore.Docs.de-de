---
title: Hochladen von Dateien in ASP.NET Core
author: guardrex
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 04e7533aa190a4875d3f66e8665fec16abec48b3
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73462940"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="01b5d-103">Hochladen von Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01b5d-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="01b5d-104">Von [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/) und [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="01b5d-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="01b5d-105">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="01b5d-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01b5d-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="01b5d-107">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-107">Security considerations</span></span>

<span data-ttu-id="01b5d-108">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="01b5d-109">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="01b5d-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="01b5d-110">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="01b5d-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="01b5d-111">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="01b5d-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="01b5d-112">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="01b5d-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="01b5d-113">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="01b5d-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="01b5d-114">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="01b5d-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="01b5d-115">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="01b5d-116">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort für hochgeladene Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="01b5d-117">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="01b5d-118">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="01b5d-119">Verwenden Sie keinen vom Benutzer angegebenen Dateinamen oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei.&dagger; Codieren Sie den nicht vertrauenswürdigen Dateinamen mit HTML, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="01b5d-120">Beispiele dafür sind die Protokollierung des Dateinamens oder die Anzeige auf der Benutzeroberfläche (Razor codiert Ausgaben automatisch mit HTML).</span><span class="sxs-lookup"><span data-stu-id="01b5d-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="01b5d-121">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="01b5d-122">Stellen Sie sicher, dass clientseitige Überprüfungen auf dem Server erfolgen.&dagger; Clientseitige Überprüfungen sind leicht zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="01b5d-123">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="01b5d-124">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="01b5d-125">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="01b5d-126">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="01b5d-127">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-128">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="01b5d-129">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="01b5d-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="01b5d-130">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="01b5d-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="01b5d-131">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="01b5d-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="01b5d-132">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="01b5d-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="01b5d-133">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="01b5d-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="01b5d-134">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="01b5d-134">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="01b5d-135">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen erfolgen, wenn Dateien von Benutzern akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="01b5d-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="01b5d-136">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="01b5d-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="01b5d-137">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="01b5d-137">Storage scenarios</span></span>

<span data-ttu-id="01b5d-138">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="01b5d-138">Common storage options for files include:</span></span>

* <span data-ttu-id="01b5d-139">Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-139">Database</span></span>

  * <span data-ttu-id="01b5d-140">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="01b5d-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="01b5d-141">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="01b5d-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="01b5d-142">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="01b5d-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="01b5d-143">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="01b5d-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="01b5d-144">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="01b5d-144">For large file uploads:</span></span>
    * <span data-ttu-id="01b5d-145">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="01b5d-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="01b5d-146">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="01b5d-147">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="01b5d-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="01b5d-148">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="01b5d-149">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="01b5d-150">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="01b5d-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="01b5d-151">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="01b5d-152">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="01b5d-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="01b5d-153">Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Blobs im Objektspeicher mithilfe von .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="01b5d-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="01b5d-154">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="01b5d-155">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="01b5d-155">File upload scenarios</span></span>

<span data-ttu-id="01b5d-156">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="01b5d-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="01b5d-157">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="01b5d-157">**Buffering**</span></span>

<span data-ttu-id="01b5d-158">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="01b5d-159">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="01b5d-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="01b5d-160">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="01b5d-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="01b5d-161">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="01b5d-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="01b5d-162">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="01b5d-163">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="01b5d-164">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="01b5d-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="01b5d-165">Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="01b5d-166">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="01b5d-166">**Streaming**</span></span>

<span data-ttu-id="01b5d-167">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="01b5d-168">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="01b5d-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="01b5d-169">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="01b5d-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="01b5d-170">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="01b5d-171">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="01b5d-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="01b5d-172">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="01b5d-173">Das folgende Beispiel veranschaulicht die Verwendung eines Razor Pages-Formulars zum Hochladen einer einzelnen Datei (*Pages/BufferedSingleFileUploadPhysical.cshtml* in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="01b5d-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="01b5d-174">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="01b5d-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="01b5d-175">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="01b5d-176">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-176">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="01b5d-177">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="01b5d-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="01b5d-178">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="01b5d-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="01b5d-179">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="01b5d-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="01b5d-180">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-180">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="01b5d-181">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="01b5d-182">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="01b5d-183">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="01b5d-184">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="01b5d-185">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="01b5d-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="01b5d-186">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="01b5d-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="01b5d-187">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="01b5d-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="01b5d-188">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-188">Applications should:</span></span>
>
> * <span data-ttu-id="01b5d-189">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="01b5d-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="01b5d-190">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="01b5d-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="01b5d-191">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="01b5d-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="01b5d-192">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="01b5d-193">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="01b5d-194">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="01b5d-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="01b5d-195">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="01b5d-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="01b5d-196">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-196">Validation</span></span>](#validation)

<span data-ttu-id="01b5d-197">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="01b5d-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="01b5d-198">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="01b5d-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="01b5d-199">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="01b5d-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="01b5d-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="01b5d-201">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-201">Binding matches form files by name.</span></span> <span data-ttu-id="01b5d-202">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="01b5d-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="01b5d-203">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="01b5d-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="01b5d-204">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="01b5d-204">The following example:</span></span>

* <span data-ttu-id="01b5d-205">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="01b5d-206">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="01b5d-207">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="01b5d-208">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="01b5d-208">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="01b5d-209">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="01b5d-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="01b5d-210">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-210">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="01b5d-211">Der an <xref:System.IO.FileStream> übergebene Pfad *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="01b5d-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="01b5d-212">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="01b5d-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="01b5d-213">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="01b5d-214">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="01b5d-215">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="01b5d-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="01b5d-216">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="01b5d-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="01b5d-218">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="01b5d-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="01b5d-219">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="01b5d-220">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="01b5d-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="01b5d-221">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="01b5d-222">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="01b5d-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="01b5d-223">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="01b5d-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="01b5d-224"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="01b5d-225">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="01b5d-226">`FileUpload` wird im Razor Pages-Formular verwendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-226">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="01b5d-227">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="01b5d-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="01b5d-228">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="01b5d-228">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="01b5d-229">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="01b5d-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="01b5d-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01b5d-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="01b5d-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="01b5d-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-232">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="01b5d-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="01b5d-233">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="01b5d-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="01b5d-234">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="01b5d-235">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="01b5d-236">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="01b5d-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="01b5d-237">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="01b5d-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="01b5d-238">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="01b5d-239">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="01b5d-239">Upload large files with streaming</span></span>

<span data-ttu-id="01b5d-240">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="01b5d-241">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="01b5d-242">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="01b5d-243">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="01b5d-244">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="01b5d-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="01b5d-245">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="01b5d-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="01b5d-246">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="01b5d-247">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="01b5d-248">In der Beispiel-App werden `GenerateAntiforgeryTokenCookieAttribute` und `DisableFormValueModelBindingAttribute` als Filter auf die Seitenanwendungsmodelle von `/StreamedSingleFileUploadDb` und `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` unter Verwendung der [Razor Pages-Konventionen](xref:razor-pages/razor-pages-conventions) angewendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="01b5d-249">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="01b5d-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="01b5d-250">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="01b5d-251">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="01b5d-252">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="01b5d-253">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="01b5d-254">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="01b5d-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="01b5d-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="01b5d-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="01b5d-256">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="01b5d-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="01b5d-257">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="01b5d-258">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-258">Validation</span></span>

<span data-ttu-id="01b5d-259">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="01b5d-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="01b5d-260">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="01b5d-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="01b5d-261">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-262">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="01b5d-263">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="01b5d-264">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="01b5d-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="01b5d-265">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="01b5d-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="01b5d-266">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="01b5d-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="01b5d-267">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="01b5d-268">Validierung von Inhalten</span><span class="sxs-lookup"><span data-stu-id="01b5d-268">Content validation</span></span>

<span data-ttu-id="01b5d-269">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="01b5d-270">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="01b5d-271">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="01b5d-272">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="01b5d-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="01b5d-273">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="01b5d-274">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="01b5d-275">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="01b5d-276">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-276">File extension validation</span></span>

<span data-ttu-id="01b5d-277">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="01b5d-278">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="01b5d-279">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="01b5d-279">File signature validation</span></span>

<span data-ttu-id="01b5d-280">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="01b5d-281">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="01b5d-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="01b5d-282">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="01b5d-283">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="01b5d-284">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="01b5d-285">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="01b5d-285">File name security</span></span>

<span data-ttu-id="01b5d-286">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="01b5d-287">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="01b5d-288">Razor versieht anzuzeigende Eigenschaftswerte automatisch mit HTML-Codierung.</span><span class="sxs-lookup"><span data-stu-id="01b5d-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="01b5d-289">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="01b5d-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="01b5d-290">Wenden Sie außerhalb von Razor stets <xref:System.Net.WebUtility.HtmlEncode*> auf Dateinameninhalte in der Anforderung eines Benutzers an.</span><span class="sxs-lookup"><span data-stu-id="01b5d-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="01b5d-291">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="01b5d-292">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="01b5d-293">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="01b5d-293">Size validation</span></span>

<span data-ttu-id="01b5d-294">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="01b5d-295">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="01b5d-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="01b5d-296">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="01b5d-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="01b5d-297">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="01b5d-298">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="01b5d-299">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="01b5d-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="01b5d-300">In Nicht-Razor-Formularen, die Daten per POST übermitteln oder `FormData` von JavaScript direkt verwenden, muss der im Formularelement oder `FormData` angegebene Name mit dem Namen des Parameters in der Aktion des Controllers übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="01b5d-301">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-301">In the following example:</span></span>

* <span data-ttu-id="01b5d-302">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="01b5d-303">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="01b5d-304">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="01b5d-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="01b5d-305">Für eine Razor Pages-Seitenhandlermethode namens `Upload`:</span><span class="sxs-lookup"><span data-stu-id="01b5d-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="01b5d-306">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="01b5d-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="01b5d-307">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="01b5d-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="01b5d-308">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="01b5d-308">Multipart body length limit</span></span>

<span data-ttu-id="01b5d-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="01b5d-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="01b5d-310">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="01b5d-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="01b5d-311">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="01b5d-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="01b5d-312">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="01b5d-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="01b5d-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="01b5d-314">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="01b5d-315">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf das Seitenmodell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="01b5d-316">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="01b5d-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="01b5d-317">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="01b5d-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="01b5d-318">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="01b5d-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="01b5d-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="01b5d-320">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="01b5d-321">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="01b5d-322">`RequestSizeLimitAttribute` kann auch mithilfe der Razor-Anweisung [@attribute](xref:mvc/views/razor#attribute) angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="01b5d-322">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="01b5d-323">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="01b5d-323">Other Kestrel limits</span></span>

<span data-ttu-id="01b5d-324">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="01b5d-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="01b5d-325">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="01b5d-326">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="01b5d-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="01b5d-327">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="01b5d-327">IIS content length limit</span></span>

<span data-ttu-id="01b5d-328">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="01b5d-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="01b5d-329">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-329">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="01b5d-330">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="01b5d-330">This setting only applies to IIS.</span></span> <span data-ttu-id="01b5d-331">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="01b5d-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="01b5d-332">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="01b5d-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="01b5d-333">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="01b5d-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="01b5d-334">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711) (Es ist nicht möglich, Dateien mit einer Größe von mehr als 2 GB hochzuladen).</span><span class="sxs-lookup"><span data-stu-id="01b5d-334">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="01b5d-335">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="01b5d-335">Troubleshoot</span></span>

<span data-ttu-id="01b5d-336">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="01b5d-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="01b5d-337">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="01b5d-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="01b5d-338">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="01b5d-339">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="01b5d-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="01b5d-340">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="01b5d-340">Connection failure</span></span>

<span data-ttu-id="01b5d-341">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="01b5d-342">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="01b5d-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="01b5d-343">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="01b5d-344">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="01b5d-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="01b5d-345">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="01b5d-346">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="01b5d-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="01b5d-347">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="01b5d-348">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-348">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="01b5d-349">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01b5d-349">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="01b5d-350">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-350">Security considerations</span></span>

<span data-ttu-id="01b5d-351">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-351">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="01b5d-352">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="01b5d-352">Attackers may attempt to:</span></span>

* <span data-ttu-id="01b5d-353">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="01b5d-353">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="01b5d-354">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="01b5d-354">Upload viruses or malware.</span></span>
* <span data-ttu-id="01b5d-355">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="01b5d-355">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="01b5d-356">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="01b5d-356">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="01b5d-357">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="01b5d-357">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="01b5d-358">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-358">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="01b5d-359">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort für hochgeladene Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-359">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="01b5d-360">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-360">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="01b5d-361">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-361">Use a safe file name determined by the app.</span></span> <span data-ttu-id="01b5d-362">Verwenden Sie keinen vom Benutzer angegebenen Dateinamen oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei.&dagger; Codieren Sie den nicht vertrauenswürdigen Dateinamen mit HTML, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-362">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="01b5d-363">Beispiele dafür sind die Protokollierung des Dateinamens oder die Anzeige auf der Benutzeroberfläche (Razor codiert Ausgaben automatisch mit HTML).</span><span class="sxs-lookup"><span data-stu-id="01b5d-363">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="01b5d-364">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-364">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="01b5d-365">Stellen Sie sicher, dass clientseitige Überprüfungen auf dem Server erfolgen.&dagger; Clientseitige Überprüfungen sind leicht zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-365">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="01b5d-366">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-366">Check the size of an uploaded file.</span></span> <span data-ttu-id="01b5d-367">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="01b5d-367">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="01b5d-368">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-368">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="01b5d-369">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-369">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="01b5d-370">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-370">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-371">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-371">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="01b5d-372">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="01b5d-372">Completely gain control of a system.</span></span>
> * <span data-ttu-id="01b5d-373">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="01b5d-373">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="01b5d-374">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="01b5d-374">Compromise user or system data.</span></span>
> * <span data-ttu-id="01b5d-375">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="01b5d-375">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="01b5d-376">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="01b5d-376">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="01b5d-377">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="01b5d-377">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="01b5d-378">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen erfolgen, wenn Dateien von Benutzern akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="01b5d-378">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="01b5d-379">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="01b5d-379">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="01b5d-380">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="01b5d-380">Storage scenarios</span></span>

<span data-ttu-id="01b5d-381">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="01b5d-381">Common storage options for files include:</span></span>

* <span data-ttu-id="01b5d-382">Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-382">Database</span></span>

  * <span data-ttu-id="01b5d-383">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="01b5d-383">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="01b5d-384">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="01b5d-384">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="01b5d-385">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="01b5d-385">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="01b5d-386">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="01b5d-386">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="01b5d-387">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="01b5d-387">For large file uploads:</span></span>
    * <span data-ttu-id="01b5d-388">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="01b5d-388">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="01b5d-389">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-389">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="01b5d-390">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="01b5d-390">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="01b5d-391">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-391">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="01b5d-392">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-392">**Never grant execute permission.**</span></span>

* <span data-ttu-id="01b5d-393">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="01b5d-393">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="01b5d-394">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-394">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="01b5d-395">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="01b5d-395">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="01b5d-396">Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Blobs im Objektspeicher mithilfe von .NET](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="01b5d-396">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="01b5d-397">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-397">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="01b5d-398">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="01b5d-398">File upload scenarios</span></span>

<span data-ttu-id="01b5d-399">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="01b5d-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="01b5d-400">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="01b5d-400">**Buffering**</span></span>

<span data-ttu-id="01b5d-401">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="01b5d-402">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="01b5d-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="01b5d-403">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="01b5d-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="01b5d-404">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="01b5d-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="01b5d-405">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="01b5d-406">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="01b5d-407">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="01b5d-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="01b5d-408">Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="01b5d-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="01b5d-409">**Streaming**</span></span>

<span data-ttu-id="01b5d-410">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="01b5d-411">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="01b5d-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="01b5d-412">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="01b5d-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="01b5d-413">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="01b5d-414">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="01b5d-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="01b5d-415">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="01b5d-416">Das folgende Beispiel veranschaulicht die Verwendung eines Razor Pages-Formulars zum Hochladen einer einzelnen Datei (*Pages/BufferedSingleFileUploadPhysical.cshtml* in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="01b5d-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="01b5d-417">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="01b5d-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="01b5d-418">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="01b5d-419">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="01b5d-420">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="01b5d-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="01b5d-421">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="01b5d-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="01b5d-422">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="01b5d-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="01b5d-423">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="01b5d-424">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="01b5d-425">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="01b5d-426">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="01b5d-427">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="01b5d-428">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="01b5d-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="01b5d-429">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="01b5d-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="01b5d-430">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="01b5d-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="01b5d-431">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-431">Applications should:</span></span>
>
> * <span data-ttu-id="01b5d-432">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="01b5d-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="01b5d-433">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="01b5d-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="01b5d-434">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="01b5d-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="01b5d-435">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="01b5d-436">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="01b5d-437">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="01b5d-437">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="01b5d-438">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="01b5d-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="01b5d-439">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-439">Validation</span></span>](#validation)

<span data-ttu-id="01b5d-440">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="01b5d-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="01b5d-441">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="01b5d-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="01b5d-442">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="01b5d-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="01b5d-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="01b5d-444">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-444">Binding matches form files by name.</span></span> <span data-ttu-id="01b5d-445">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="01b5d-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="01b5d-446">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="01b5d-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="01b5d-447">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="01b5d-447">The following example:</span></span>

* <span data-ttu-id="01b5d-448">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="01b5d-449">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="01b5d-450">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="01b5d-451">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="01b5d-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="01b5d-452">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="01b5d-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="01b5d-453">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="01b5d-454">Der an <xref:System.IO.FileStream> übergebene Pfad *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="01b5d-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="01b5d-455">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="01b5d-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="01b5d-456">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="01b5d-457">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="01b5d-458">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="01b5d-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="01b5d-459">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="01b5d-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="01b5d-461">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="01b5d-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="01b5d-462">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="01b5d-463">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="01b5d-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="01b5d-464">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="01b5d-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="01b5d-465">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="01b5d-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="01b5d-466">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="01b5d-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="01b5d-467"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="01b5d-468">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="01b5d-469">`FileUpload` wird im Razor Pages-Formular verwendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="01b5d-470">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="01b5d-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="01b5d-471">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="01b5d-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="01b5d-472">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="01b5d-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="01b5d-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="01b5d-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="01b5d-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="01b5d-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-475">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="01b5d-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="01b5d-476">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="01b5d-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="01b5d-477">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="01b5d-478">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="01b5d-479">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="01b5d-479">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="01b5d-480">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="01b5d-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="01b5d-481">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="01b5d-482">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="01b5d-482">Upload large files with streaming</span></span>

<span data-ttu-id="01b5d-483">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="01b5d-484">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="01b5d-485">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="01b5d-486">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="01b5d-487">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="01b5d-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="01b5d-488">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="01b5d-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="01b5d-489">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="01b5d-490">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="01b5d-491">In der Beispiel-App werden `GenerateAntiforgeryTokenCookieAttribute` und `DisableFormValueModelBindingAttribute` als Filter auf die Seitenanwendungsmodelle von `/StreamedSingleFileUploadDb` und `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` unter Verwendung der [Razor Pages-Konventionen](xref:razor-pages/razor-pages-conventions) angewendet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="01b5d-492">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="01b5d-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="01b5d-493">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="01b5d-494">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="01b5d-495">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="01b5d-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="01b5d-496">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="01b5d-497">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="01b5d-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="01b5d-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="01b5d-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="01b5d-499">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="01b5d-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="01b5d-500">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="01b5d-501">Validierung</span><span class="sxs-lookup"><span data-stu-id="01b5d-501">Validation</span></span>

<span data-ttu-id="01b5d-502">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="01b5d-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="01b5d-503">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="01b5d-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="01b5d-504">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="01b5d-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="01b5d-505">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="01b5d-506">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="01b5d-507">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="01b5d-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="01b5d-508">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="01b5d-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="01b5d-509">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="01b5d-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="01b5d-510">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="01b5d-511">Validierung von Inhalten</span><span class="sxs-lookup"><span data-stu-id="01b5d-511">Content validation</span></span>

<span data-ttu-id="01b5d-512">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="01b5d-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="01b5d-513">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="01b5d-514">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="01b5d-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="01b5d-515">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="01b5d-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="01b5d-516">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="01b5d-517">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="01b5d-518">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="01b5d-519">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-519">File extension validation</span></span>

<span data-ttu-id="01b5d-520">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="01b5d-521">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="01b5d-522">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="01b5d-522">File signature validation</span></span>

<span data-ttu-id="01b5d-523">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="01b5d-524">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="01b5d-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="01b5d-525">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="01b5d-526">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="01b5d-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="01b5d-527">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="01b5d-528">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="01b5d-528">File name security</span></span>

<span data-ttu-id="01b5d-529">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="01b5d-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="01b5d-530">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="01b5d-531">Razor versieht anzuzeigende Eigenschaftswerte automatisch mit HTML-Codierung.</span><span class="sxs-lookup"><span data-stu-id="01b5d-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="01b5d-532">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="01b5d-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="01b5d-533">Wenden Sie außerhalb von Razor stets <xref:System.Net.WebUtility.HtmlEncode*> auf Dateinameninhalte in der Anforderung eines Benutzers an.</span><span class="sxs-lookup"><span data-stu-id="01b5d-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="01b5d-534">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="01b5d-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="01b5d-535">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="01b5d-536">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="01b5d-536">Size validation</span></span>

<span data-ttu-id="01b5d-537">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b5d-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="01b5d-538">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="01b5d-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="01b5d-539">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="01b5d-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="01b5d-540">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="01b5d-541">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="01b5d-542">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="01b5d-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="01b5d-543">In Nicht-Razor-Formularen, die Daten per POST übermitteln oder `FormData` von JavaScript direkt verwenden, muss der im Formularelement oder `FormData` angegebene Name mit dem Namen des Parameters in der Aktion des Controllers übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="01b5d-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="01b5d-544">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="01b5d-544">In the following example:</span></span>

* <span data-ttu-id="01b5d-545">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="01b5d-546">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="01b5d-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="01b5d-547">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="01b5d-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="01b5d-548">Für eine Razor Pages-Seitenhandlermethode namens `Upload`:</span><span class="sxs-lookup"><span data-stu-id="01b5d-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="01b5d-549">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="01b5d-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="01b5d-550">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="01b5d-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="01b5d-551">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="01b5d-551">Multipart body length limit</span></span>

<span data-ttu-id="01b5d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="01b5d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="01b5d-553">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="01b5d-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="01b5d-554">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="01b5d-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="01b5d-555">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="01b5d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="01b5d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="01b5d-557">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="01b5d-558">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf das Seitenmodell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="01b5d-559">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="01b5d-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="01b5d-560">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="01b5d-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="01b5d-561">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="01b5d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="01b5d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="01b5d-563">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="01b5d-564">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="01b5d-565">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="01b5d-565">Other Kestrel limits</span></span>

<span data-ttu-id="01b5d-566">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="01b5d-566">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="01b5d-567">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="01b5d-567">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="01b5d-568">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="01b5d-568">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="01b5d-569">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="01b5d-569">IIS content length limit</span></span>

<span data-ttu-id="01b5d-570">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="01b5d-570">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="01b5d-571">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="01b5d-571">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="01b5d-572">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="01b5d-572">This setting only applies to IIS.</span></span> <span data-ttu-id="01b5d-573">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="01b5d-573">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="01b5d-574">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="01b5d-574">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="01b5d-575">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="01b5d-575">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="01b5d-576">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711) (Es ist nicht möglich, Dateien mit einer Größe von mehr als 2 GB hochzuladen).</span><span class="sxs-lookup"><span data-stu-id="01b5d-576">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="01b5d-577">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="01b5d-577">Troubleshoot</span></span>

<span data-ttu-id="01b5d-578">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="01b5d-578">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="01b5d-579">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="01b5d-579">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="01b5d-580">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="01b5d-580">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="01b5d-581">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="01b5d-581">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="01b5d-582">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="01b5d-582">Connection failure</span></span>

<span data-ttu-id="01b5d-583">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="01b5d-583">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="01b5d-584">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="01b5d-584">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="01b5d-585">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="01b5d-585">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="01b5d-586">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="01b5d-586">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="01b5d-587">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-587">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="01b5d-588">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="01b5d-588">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="01b5d-589">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="01b5d-589">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="01b5d-590">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="01b5d-590">Additional resources</span></span>

* [<span data-ttu-id="01b5d-591">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="01b5d-591">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="01b5d-592">Azure-Sicherheit: Sicherheitsrahmen: Eingabeüberprüfung | Risikominderung</span><span class="sxs-lookup"><span data-stu-id="01b5d-592">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="01b5d-593">Azure-Cloudentwurfsmuster: Muster „Valetschlüssel“</span><span class="sxs-lookup"><span data-stu-id="01b5d-593">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
