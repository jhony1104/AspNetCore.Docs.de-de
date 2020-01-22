---
title: Hochladen von Dateien in ASP.NET Core
author: guardrex
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: b5433576ff3e997e6d80201236be2d8463a52d07
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829230"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="cabab-103">Hochladen von Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cabab-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="cabab-104">Von [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/) und [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="cabab-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cabab-105">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="cabab-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cabab-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="cabab-107">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="cabab-107">Security considerations</span></span>

<span data-ttu-id="cabab-108">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="cabab-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="cabab-109">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cabab-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="cabab-110">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="cabab-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="cabab-111">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="cabab-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="cabab-112">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="cabab-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="cabab-113">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="cabab-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="cabab-114">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="cabab-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="cabab-115">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="cabab-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="cabab-116">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort für hochgeladene Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="cabab-117">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="cabab-118">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="cabab-119">Verwenden Sie keinen vom Benutzer angegebenen Dateinamen oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei.&dagger; Codieren Sie den nicht vertrauenswürdigen Dateinamen mit HTML, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="cabab-120">Beispiele dafür sind die Protokollierung des Dateinamens oder die Anzeige auf der Benutzeroberfläche (Razor codiert Ausgaben automatisch mit HTML).</span><span class="sxs-lookup"><span data-stu-id="cabab-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="cabab-121">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="cabab-122">Stellen Sie sicher, dass clientseitige Überprüfungen auf dem Server erfolgen.&dagger; Clientseitige Überprüfungen sind leicht zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="cabab-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="cabab-123">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="cabab-124">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="cabab-125">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="cabab-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="cabab-126">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="cabab-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="cabab-127">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="cabab-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-128">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="cabab-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="cabab-129">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="cabab-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="cabab-130">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="cabab-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="cabab-131">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="cabab-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="cabab-132">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="cabab-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="cabab-133">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="cabab-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="cabab-134">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="cabab-134">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="cabab-135">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen erfolgen, wenn Dateien von Benutzern akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="cabab-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="cabab-136">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="cabab-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="cabab-137">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="cabab-137">Storage scenarios</span></span>

<span data-ttu-id="cabab-138">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="cabab-138">Common storage options for files include:</span></span>

* <span data-ttu-id="cabab-139">Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-139">Database</span></span>

  * <span data-ttu-id="cabab-140">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="cabab-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="cabab-141">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="cabab-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="cabab-142">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="cabab-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="cabab-143">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="cabab-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="cabab-144">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="cabab-144">For large file uploads:</span></span>
    * <span data-ttu-id="cabab-145">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="cabab-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="cabab-146">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="cabab-147">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="cabab-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="cabab-148">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="cabab-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="cabab-149">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="cabab-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="cabab-150">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="cabab-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="cabab-151">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cabab-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="cabab-152">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="cabab-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="cabab-153">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .NET zum Erstellen eines Blobs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cabab-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="cabab-154">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="cabab-155">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="cabab-155">File upload scenarios</span></span>

<span data-ttu-id="cabab-156">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="cabab-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="cabab-157">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="cabab-157">**Buffering**</span></span>

<span data-ttu-id="cabab-158">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="cabab-159">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="cabab-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="cabab-160">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="cabab-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="cabab-161">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="cabab-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="cabab-162">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="cabab-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="cabab-163">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="cabab-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="cabab-164">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="cabab-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="cabab-165">Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="cabab-166">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="cabab-166">**Streaming**</span></span>

<span data-ttu-id="cabab-167">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cabab-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="cabab-168">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="cabab-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="cabab-169">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="cabab-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="cabab-170">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cabab-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="cabab-171">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="cabab-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="cabab-172">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="cabab-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="cabab-173">Das folgende Beispiel veranschaulicht die Verwendung eines Razor Pages-Formulars zum Hochladen einer einzelnen Datei (*Pages/BufferedSingleFileUploadPhysical.cshtml* in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="cabab-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="cabab-174">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="cabab-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="cabab-175">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="cabab-176">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="cabab-176">There's no validation.</span></span>

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

<span data-ttu-id="cabab-177">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="cabab-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="cabab-178">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="cabab-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="cabab-179">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="cabab-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="cabab-180">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-180">For example:</span></span>

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

<span data-ttu-id="cabab-181">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="cabab-182">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="cabab-183">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="cabab-184">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="cabab-185">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="cabab-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="cabab-186">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="cabab-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="cabab-187">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="cabab-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="cabab-188">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="cabab-188">Applications should:</span></span>
>
> * <span data-ttu-id="cabab-189">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="cabab-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="cabab-190">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="cabab-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="cabab-191">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="cabab-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="cabab-192">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="cabab-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="cabab-193">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="cabab-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="cabab-194">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="cabab-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cabab-195">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="cabab-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cabab-196">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-196">Validation</span></span>](#validation)

<span data-ttu-id="cabab-197">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="cabab-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="cabab-198">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="cabab-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="cabab-199">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="cabab-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="cabab-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="cabab-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="cabab-201">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="cabab-201">Binding matches form files by name.</span></span> <span data-ttu-id="cabab-202">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="cabab-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="cabab-203">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="cabab-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="cabab-204">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cabab-204">The following example:</span></span>

* <span data-ttu-id="cabab-205">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="cabab-206">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="cabab-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="cabab-207">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="cabab-208">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="cabab-208">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="cabab-209">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cabab-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="cabab-210">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="cabab-210">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="cabab-211">Der an <xref:System.IO.FileStream> übergebene Pfad *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="cabab-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="cabab-212">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="cabab-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="cabab-213">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="cabab-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="cabab-214">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="cabab-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cabab-215">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="cabab-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="cabab-216">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="cabab-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="cabab-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="cabab-218">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="cabab-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="cabab-219">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="cabab-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="cabab-220">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="cabab-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="cabab-221">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="cabab-222">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="cabab-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="cabab-223">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="cabab-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="cabab-224"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="cabab-225">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="cabab-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="cabab-226">`FileUpload` wird im Razor Pages-Formular verwendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-226">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="cabab-227">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="cabab-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="cabab-228">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="cabab-228">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="cabab-229">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="cabab-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="cabab-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cabab-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="cabab-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="cabab-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-232">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="cabab-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="cabab-233">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="cabab-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="cabab-234">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="cabab-235">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="cabab-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="cabab-236">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="cabab-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cabab-237">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="cabab-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cabab-238">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="cabab-239">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="cabab-239">Upload large files with streaming</span></span>

<span data-ttu-id="cabab-240">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="cabab-241">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="cabab-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="cabab-242">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="cabab-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="cabab-243">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="cabab-244">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="cabab-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="cabab-245">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="cabab-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="cabab-246">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="cabab-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="cabab-247">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="cabab-248">In der Beispiel-App werden `GenerateAntiforgeryTokenCookieAttribute` und `DisableFormValueModelBindingAttribute` als Filter auf die Seitenanwendungsmodelle von `/StreamedSingleFileUploadDb` und `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` unter Verwendung der [Razor Pages-Konventionen](xref:razor-pages/razor-pages-conventions) angewendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="cabab-249">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="cabab-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="cabab-250">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="cabab-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="cabab-251">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="cabab-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="cabab-252">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cabab-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="cabab-253">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="cabab-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="cabab-254">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="cabab-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="cabab-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="cabab-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="cabab-256">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="cabab-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="cabab-257">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="cabab-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="cabab-258">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-258">Validation</span></span>

<span data-ttu-id="cabab-259">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="cabab-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="cabab-260">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="cabab-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="cabab-261">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-262">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="cabab-263">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="cabab-264">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="cabab-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="cabab-265">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="cabab-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="cabab-266">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="cabab-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="cabab-267">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="cabab-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="cabab-268">Inhaltsprüfung</span><span class="sxs-lookup"><span data-stu-id="cabab-268">Content validation</span></span>

<span data-ttu-id="cabab-269">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="cabab-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="cabab-270">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="cabab-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="cabab-271">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="cabab-272">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="cabab-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="cabab-273">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="cabab-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="cabab-274">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="cabab-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="cabab-275">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="cabab-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="cabab-276">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="cabab-276">File extension validation</span></span>

<span data-ttu-id="cabab-277">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="cabab-278">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="cabab-279">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="cabab-279">File signature validation</span></span>

<span data-ttu-id="cabab-280">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="cabab-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="cabab-281">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="cabab-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="cabab-282">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="cabab-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="cabab-283">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="cabab-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="cabab-284">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="cabab-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="cabab-285">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="cabab-285">File name security</span></span>

<span data-ttu-id="cabab-286">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="cabab-287">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cabab-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="cabab-288">Razor versieht anzuzeigende Eigenschaftswerte automatisch mit HTML-Codierung.</span><span class="sxs-lookup"><span data-stu-id="cabab-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="cabab-289">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="cabab-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="cabab-290">Wenden Sie außerhalb von Razor stets <xref:System.Net.WebUtility.HtmlEncode*> auf Dateinameninhalte in der Anforderung eines Benutzers an.</span><span class="sxs-lookup"><span data-stu-id="cabab-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="cabab-291">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="cabab-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="cabab-292">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="cabab-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="cabab-293">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="cabab-293">Size validation</span></span>

<span data-ttu-id="cabab-294">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="cabab-295">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="cabab-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="cabab-296">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="cabab-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="cabab-297">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="cabab-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="cabab-298">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="cabab-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="cabab-299">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="cabab-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="cabab-300">In Nicht-Razor-Formularen, die Daten per POST übermitteln oder `FormData` von JavaScript direkt verwenden, muss der im Formularelement oder `FormData` angegebene Name mit dem Namen des Parameters in der Aktion des Controllers übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cabab-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="cabab-301">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-301">In the following example:</span></span>

* <span data-ttu-id="cabab-302">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cabab-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="cabab-303">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cabab-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="cabab-304">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="cabab-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="cabab-305">Für eine Razor Pages-Seitenhandlermethode namens `Upload`:</span><span class="sxs-lookup"><span data-stu-id="cabab-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="cabab-306">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="cabab-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="cabab-307">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="cabab-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="cabab-308">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="cabab-308">Multipart body length limit</span></span>

<span data-ttu-id="cabab-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="cabab-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="cabab-310">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="cabab-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="cabab-311">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="cabab-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="cabab-312">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="cabab-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="cabab-314">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-315">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf das Seitenmodell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="cabab-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="cabab-316">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="cabab-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="cabab-317">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cabab-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="cabab-318">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="cabab-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="cabab-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="cabab-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="cabab-320">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-321">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="cabab-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="cabab-322">`RequestSizeLimitAttribute` kann auch mithilfe der Razor-Anweisung [`@attribute`](xref:mvc/views/razor#attribute) angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="cabab-322">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="cabab-323">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="cabab-323">Other Kestrel limits</span></span>

<span data-ttu-id="cabab-324">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="cabab-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="cabab-325">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="cabab-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="cabab-326">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="cabab-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="cabab-327">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="cabab-327">IIS content length limit</span></span>

<span data-ttu-id="cabab-328">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cabab-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="cabab-329">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="cabab-329">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="cabab-330">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="cabab-330">This setting only applies to IIS.</span></span> <span data-ttu-id="cabab-331">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="cabab-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="cabab-332">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="cabab-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="cabab-333">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="cabab-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="cabab-334">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711) (Upload von Dateien größer als 2 GB ist nicht möglich).</span><span class="sxs-lookup"><span data-stu-id="cabab-334">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cabab-335">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="cabab-335">Troubleshoot</span></span>

<span data-ttu-id="cabab-336">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="cabab-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="cabab-337">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="cabab-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="cabab-338">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="cabab-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="cabab-339">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="cabab-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="cabab-340">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="cabab-340">Connection failure</span></span>

<span data-ttu-id="cabab-341">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="cabab-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="cabab-342">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="cabab-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="cabab-343">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="cabab-344">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="cabab-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="cabab-345">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="cabab-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="cabab-346">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="cabab-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="cabab-347">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="cabab-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="cabab-348">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="cabab-348">Stream was too long</span></span>

<span data-ttu-id="cabab-349">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="cabab-349">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="cabab-350">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="cabab-350">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="cabab-351">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="cabab-351">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cabab-352">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-352">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="cabab-353">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cabab-353">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="cabab-354">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="cabab-354">Security considerations</span></span>

<span data-ttu-id="cabab-355">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="cabab-355">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="cabab-356">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cabab-356">Attackers may attempt to:</span></span>

* <span data-ttu-id="cabab-357">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="cabab-357">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="cabab-358">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="cabab-358">Upload viruses or malware.</span></span>
* <span data-ttu-id="cabab-359">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="cabab-359">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="cabab-360">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="cabab-360">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="cabab-361">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="cabab-361">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="cabab-362">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="cabab-362">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="cabab-363">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort für hochgeladene Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-363">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="cabab-364">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-364">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="cabab-365">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-365">Use a safe file name determined by the app.</span></span> <span data-ttu-id="cabab-366">Verwenden Sie keinen vom Benutzer angegebenen Dateinamen oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei.&dagger; Codieren Sie den nicht vertrauenswürdigen Dateinamen mit HTML, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-366">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="cabab-367">Beispiele dafür sind die Protokollierung des Dateinamens oder die Anzeige auf der Benutzeroberfläche (Razor codiert Ausgaben automatisch mit HTML).</span><span class="sxs-lookup"><span data-stu-id="cabab-367">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="cabab-368">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-368">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="cabab-369">Stellen Sie sicher, dass clientseitige Überprüfungen auf dem Server erfolgen.&dagger; Clientseitige Überprüfungen sind leicht zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="cabab-369">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="cabab-370">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-370">Check the size of an uploaded file.</span></span> <span data-ttu-id="cabab-371">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="cabab-371">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="cabab-372">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="cabab-372">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="cabab-373">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="cabab-373">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="cabab-374">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="cabab-374">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-375">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="cabab-375">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="cabab-376">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="cabab-376">Completely gain control of a system.</span></span>
> * <span data-ttu-id="cabab-377">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="cabab-377">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="cabab-378">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="cabab-378">Compromise user or system data.</span></span>
> * <span data-ttu-id="cabab-379">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="cabab-379">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="cabab-380">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="cabab-380">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="cabab-381">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="cabab-381">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="cabab-382">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen erfolgen, wenn Dateien von Benutzern akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="cabab-382">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="cabab-383">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="cabab-383">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="cabab-384">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="cabab-384">Storage scenarios</span></span>

<span data-ttu-id="cabab-385">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="cabab-385">Common storage options for files include:</span></span>

* <span data-ttu-id="cabab-386">Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-386">Database</span></span>

  * <span data-ttu-id="cabab-387">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="cabab-387">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="cabab-388">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="cabab-388">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="cabab-389">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="cabab-389">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="cabab-390">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="cabab-390">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="cabab-391">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="cabab-391">For large file uploads:</span></span>
    * <span data-ttu-id="cabab-392">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="cabab-392">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="cabab-393">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-393">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="cabab-394">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="cabab-394">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="cabab-395">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="cabab-395">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="cabab-396">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="cabab-396">**Never grant execute permission.**</span></span>

* <span data-ttu-id="cabab-397">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="cabab-397">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="cabab-398">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cabab-398">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="cabab-399">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="cabab-399">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="cabab-400">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .NET zum Erstellen eines Blobs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="cabab-400">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="cabab-401">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-401">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="cabab-402">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="cabab-402">File upload scenarios</span></span>

<span data-ttu-id="cabab-403">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="cabab-403">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="cabab-404">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="cabab-404">**Buffering**</span></span>

<span data-ttu-id="cabab-405">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-405">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="cabab-406">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="cabab-406">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="cabab-407">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="cabab-407">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="cabab-408">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="cabab-408">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="cabab-409">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="cabab-409">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="cabab-410">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="cabab-410">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="cabab-411">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="cabab-411">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="cabab-412">Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-412">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="cabab-413">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="cabab-413">**Streaming**</span></span>

<span data-ttu-id="cabab-414">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cabab-414">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="cabab-415">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="cabab-415">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="cabab-416">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="cabab-416">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="cabab-417">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cabab-417">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="cabab-418">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="cabab-418">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="cabab-419">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="cabab-419">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="cabab-420">Das folgende Beispiel veranschaulicht die Verwendung eines Razor Pages-Formulars zum Hochladen einer einzelnen Datei (*Pages/BufferedSingleFileUploadPhysical.cshtml* in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="cabab-420">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="cabab-421">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="cabab-421">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="cabab-422">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-422">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="cabab-423">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="cabab-423">There's no validation.</span></span>

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

<span data-ttu-id="cabab-424">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="cabab-424">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="cabab-425">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="cabab-425">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="cabab-426">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="cabab-426">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="cabab-427">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-427">For example:</span></span>

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

<span data-ttu-id="cabab-428">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-428">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="cabab-429">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-429">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="cabab-430">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-430">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="cabab-431">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-431">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="cabab-432">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="cabab-432">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="cabab-433">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="cabab-433">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="cabab-434">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="cabab-434">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="cabab-435">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="cabab-435">Applications should:</span></span>
>
> * <span data-ttu-id="cabab-436">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="cabab-436">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="cabab-437">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="cabab-437">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="cabab-438">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="cabab-438">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="cabab-439">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="cabab-439">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="cabab-440">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="cabab-440">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="cabab-441">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="cabab-441">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cabab-442">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="cabab-442">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cabab-443">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-443">Validation</span></span>](#validation)

<span data-ttu-id="cabab-444">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="cabab-444">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="cabab-445">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="cabab-445">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="cabab-446">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="cabab-446">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="cabab-447">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="cabab-447">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="cabab-448">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="cabab-448">Binding matches form files by name.</span></span> <span data-ttu-id="cabab-449">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="cabab-449">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="cabab-450">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="cabab-450">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="cabab-451">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cabab-451">The following example:</span></span>

* <span data-ttu-id="cabab-452">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-452">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="cabab-453">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="cabab-453">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="cabab-454">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-454">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="cabab-455">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="cabab-455">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="cabab-456">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cabab-456">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="cabab-457">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="cabab-457">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="cabab-458">Der an <xref:System.IO.FileStream> übergebene Pfad *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="cabab-458">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="cabab-459">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="cabab-459">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="cabab-460">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="cabab-460">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="cabab-461">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="cabab-461">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="cabab-462">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="cabab-462">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="cabab-463">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="cabab-463">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-464">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="cabab-464">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="cabab-465">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="cabab-465">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="cabab-466">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="cabab-466">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="cabab-467">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="cabab-467">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="cabab-468">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="cabab-468">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="cabab-469">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="cabab-469">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="cabab-470">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="cabab-470">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="cabab-471"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-471"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="cabab-472">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="cabab-472">The prior example uses a bound model property.</span></span>

<span data-ttu-id="cabab-473">`FileUpload` wird im Razor Pages-Formular verwendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-473">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="cabab-474">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="cabab-474">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="cabab-475">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="cabab-475">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="cabab-476">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="cabab-476">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="cabab-477">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cabab-477">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="cabab-478">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="cabab-478">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-479">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="cabab-479">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="cabab-480">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="cabab-480">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="cabab-481">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-481">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="cabab-482">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="cabab-482">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="cabab-483">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="cabab-483">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="cabab-484">Überlegungen zur Sicherheit</span><span class="sxs-lookup"><span data-stu-id="cabab-484">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="cabab-485">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-485">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="cabab-486">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="cabab-486">Upload large files with streaming</span></span>

<span data-ttu-id="cabab-487">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="cabab-487">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="cabab-488">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="cabab-488">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="cabab-489">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="cabab-489">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="cabab-490">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-490">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="cabab-491">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="cabab-491">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="cabab-492">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="cabab-492">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="cabab-493">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="cabab-493">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="cabab-494">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-494">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="cabab-495">In der Beispiel-App werden `GenerateAntiforgeryTokenCookieAttribute` und `DisableFormValueModelBindingAttribute` als Filter auf die Seitenanwendungsmodelle von `/StreamedSingleFileUploadDb` und `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` unter Verwendung der [Razor Pages-Konventionen](xref:razor-pages/razor-pages-conventions) angewendet:</span><span class="sxs-lookup"><span data-stu-id="cabab-495">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="cabab-496">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="cabab-496">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="cabab-497">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="cabab-497">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="cabab-498">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="cabab-498">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="cabab-499">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cabab-499">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="cabab-500">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="cabab-500">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="cabab-501">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="cabab-501">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="cabab-502">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="cabab-502">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="cabab-503">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="cabab-503">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="cabab-504">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="cabab-504">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="cabab-505">Validierung</span><span class="sxs-lookup"><span data-stu-id="cabab-505">Validation</span></span>

<span data-ttu-id="cabab-506">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="cabab-506">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="cabab-507">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="cabab-507">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="cabab-508">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="cabab-508">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="cabab-509">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-509">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="cabab-510">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-510">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="cabab-511">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="cabab-511">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="cabab-512">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="cabab-512">Fully understand the implementation.</span></span>
> * <span data-ttu-id="cabab-513">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="cabab-513">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="cabab-514">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="cabab-514">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="cabab-515">Inhaltsprüfung</span><span class="sxs-lookup"><span data-stu-id="cabab-515">Content validation</span></span>

<span data-ttu-id="cabab-516">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="cabab-516">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="cabab-517">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="cabab-517">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="cabab-518">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cabab-518">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="cabab-519">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="cabab-519">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="cabab-520">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="cabab-520">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="cabab-521">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="cabab-521">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="cabab-522">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="cabab-522">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="cabab-523">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="cabab-523">File extension validation</span></span>

<span data-ttu-id="cabab-524">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-524">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="cabab-525">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-525">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="cabab-526">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="cabab-526">File signature validation</span></span>

<span data-ttu-id="cabab-527">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="cabab-527">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="cabab-528">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="cabab-528">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="cabab-529">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="cabab-529">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="cabab-530">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="cabab-530">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="cabab-531">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="cabab-531">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="cabab-532">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="cabab-532">File name security</span></span>

<span data-ttu-id="cabab-533">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="cabab-533">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="cabab-534">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cabab-534">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="cabab-535">Razor versieht anzuzeigende Eigenschaftswerte automatisch mit HTML-Codierung.</span><span class="sxs-lookup"><span data-stu-id="cabab-535">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="cabab-536">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="cabab-536">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="cabab-537">Wenden Sie außerhalb von Razor stets <xref:System.Net.WebUtility.HtmlEncode*> auf Dateinameninhalte in der Anforderung eines Benutzers an.</span><span class="sxs-lookup"><span data-stu-id="cabab-537">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="cabab-538">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="cabab-538">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="cabab-539">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="cabab-539">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="cabab-540">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="cabab-540">Size validation</span></span>

<span data-ttu-id="cabab-541">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="cabab-541">Limit the size of uploaded files.</span></span>

<span data-ttu-id="cabab-542">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="cabab-542">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="cabab-543">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="cabab-543">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="cabab-544">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="cabab-544">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="cabab-545">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="cabab-545">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="cabab-546">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="cabab-546">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="cabab-547">In Nicht-Razor-Formularen, die Daten per POST übermitteln oder `FormData` von JavaScript direkt verwenden, muss der im Formularelement oder `FormData` angegebene Name mit dem Namen des Parameters in der Aktion des Controllers übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cabab-547">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="cabab-548">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cabab-548">In the following example:</span></span>

* <span data-ttu-id="cabab-549">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cabab-549">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="cabab-550">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cabab-550">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="cabab-551">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="cabab-551">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="cabab-552">Für eine Razor Pages-Seitenhandlermethode namens `Upload`:</span><span class="sxs-lookup"><span data-stu-id="cabab-552">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="cabab-553">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="cabab-553">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="cabab-554">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="cabab-554">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="cabab-555">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="cabab-555">Multipart body length limit</span></span>

<span data-ttu-id="cabab-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="cabab-556"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="cabab-557">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="cabab-557">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="cabab-558">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="cabab-558">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="cabab-559">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-559">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="cabab-560"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="cabab-561">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-561">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-562">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf das Seitenmodell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="cabab-562">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="cabab-563">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="cabab-563">Kestrel maximum request body size</span></span>

<span data-ttu-id="cabab-564">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cabab-564">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="cabab-565">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="cabab-565">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="cabab-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="cabab-566"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="cabab-567">Wenden Sie in einer Razor Pages-Anwendung den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="cabab-567">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="cabab-568">Wenden Sie in einer Razor Pages- oder MVC-App den Filter auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="cabab-568">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="cabab-569">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="cabab-569">Other Kestrel limits</span></span>

<span data-ttu-id="cabab-570">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="cabab-570">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="cabab-571">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="cabab-571">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="cabab-572">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="cabab-572">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="cabab-573">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="cabab-573">IIS content length limit</span></span>

<span data-ttu-id="cabab-574">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="cabab-574">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="cabab-575">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="cabab-575">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="cabab-576">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="cabab-576">This setting only applies to IIS.</span></span> <span data-ttu-id="cabab-577">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="cabab-577">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="cabab-578">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="cabab-578">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="cabab-579">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="cabab-579">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="cabab-580">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711) (Upload von Dateien größer als 2 GB ist nicht möglich).</span><span class="sxs-lookup"><span data-stu-id="cabab-580">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="cabab-581">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="cabab-581">Troubleshoot</span></span>

<span data-ttu-id="cabab-582">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="cabab-582">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="cabab-583">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="cabab-583">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="cabab-584">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="cabab-584">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="cabab-585">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="cabab-585">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="cabab-586">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="cabab-586">Connection failure</span></span>

<span data-ttu-id="cabab-587">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="cabab-587">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="cabab-588">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="cabab-588">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="cabab-589">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="cabab-589">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="cabab-590">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="cabab-590">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="cabab-591">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="cabab-591">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="cabab-592">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="cabab-592">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="cabab-593">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="cabab-593">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="cabab-594">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="cabab-594">Stream was too long</span></span>

<span data-ttu-id="cabab-595">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="cabab-595">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="cabab-596">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="cabab-596">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="cabab-597">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="cabab-597">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="cabab-598">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cabab-598">Additional resources</span></span>

* [<span data-ttu-id="cabab-599">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="cabab-599">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="cabab-600">Azure-Sicherheit: Security Frame: Eingabeüberprüfung | Risikominderung</span><span class="sxs-lookup"><span data-stu-id="cabab-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="cabab-601">Azure-Cloudentwurfsmuster: Muster „Valetschlüssel“</span><span class="sxs-lookup"><span data-stu-id="cabab-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
