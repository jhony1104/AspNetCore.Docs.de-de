---
<span data-ttu-id="2f08e-101">Title: Autor: Beschreibung: monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="2f08e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2f08e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2f08e-102">'Blazor'</span></span>
- <span data-ttu-id="2f08e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2f08e-103">'Identity'</span></span>
- <span data-ttu-id="2f08e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2f08e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2f08e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2f08e-105">'Razor'</span></span>
- <span data-ttu-id="2f08e-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="2f08e-106">'SignalR' uid:</span></span> 

---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="2f08e-107">Hochladen von Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f08e-107">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="2f08e-108">Von [Steve Smith](https://ardalis.com/) und [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="2f08e-108">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f08e-109">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-109">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="2f08e-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f08e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="2f08e-111">Sicherheitshinweise</span><span class="sxs-lookup"><span data-stu-id="2f08e-111">Security considerations</span></span>

<span data-ttu-id="2f08e-112">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-112">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="2f08e-113">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2f08e-113">Attackers may attempt to:</span></span>

* <span data-ttu-id="2f08e-114">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="2f08e-114">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="2f08e-115">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="2f08e-115">Upload viruses or malware.</span></span>
* <span data-ttu-id="2f08e-116">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="2f08e-116">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="2f08e-117">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="2f08e-117">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="2f08e-118">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="2f08e-118">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="2f08e-119">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-119">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="2f08e-120">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-120">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="2f08e-121">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-121">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="2f08e-122">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-122">Use a safe file name determined by the app.</span></span> <span data-ttu-id="2f08e-123">Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-123">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="2f08e-124">Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).</span><span class="sxs-lookup"><span data-stu-id="2f08e-124">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="2f08e-125">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-125">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="2f08e-126">Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-126">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="2f08e-127">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-127">Check the size of an uploaded file.</span></span> <span data-ttu-id="2f08e-128">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-128">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="2f08e-129">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-129">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="2f08e-130">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-130">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="2f08e-131">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-131">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-132">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-132">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="2f08e-133">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="2f08e-133">Completely gain control of a system.</span></span>
> * <span data-ttu-id="2f08e-134">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="2f08e-134">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="2f08e-135">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="2f08e-135">Compromise user or system data.</span></span>
> * <span data-ttu-id="2f08e-136">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="2f08e-136">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="2f08e-137">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="2f08e-137">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="2f08e-138">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="2f08e-138">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="2f08e-139">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="2f08e-139">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="2f08e-140">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="2f08e-140">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="2f08e-141">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="2f08e-141">Storage scenarios</span></span>

<span data-ttu-id="2f08e-142">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="2f08e-142">Common storage options for files include:</span></span>

* <span data-ttu-id="2f08e-143">Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-143">Database</span></span>

  * <span data-ttu-id="2f08e-144">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="2f08e-144">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="2f08e-145">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="2f08e-145">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="2f08e-146">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="2f08e-146">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="2f08e-147">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="2f08e-147">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="2f08e-148">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="2f08e-148">For large file uploads:</span></span>
    * <span data-ttu-id="2f08e-149">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="2f08e-149">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="2f08e-150">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-150">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="2f08e-151">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="2f08e-151">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="2f08e-152">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-152">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="2f08e-153">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-153">**Never grant execute permission.**</span></span>

* <span data-ttu-id="2f08e-154">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="2f08e-154">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="2f08e-155">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-155">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="2f08e-156">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="2f08e-156">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="2f08e-157">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="2f08e-157">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="2f08e-158">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="2f08e-158">File upload scenarios</span></span>

<span data-ttu-id="2f08e-159">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="2f08e-159">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="2f08e-160">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="2f08e-160">**Buffering**</span></span>

<span data-ttu-id="2f08e-161">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-161">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="2f08e-162">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="2f08e-162">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="2f08e-163">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="2f08e-163">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="2f08e-164">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="2f08e-164">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="2f08e-165">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-165">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="2f08e-166">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-166">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="2f08e-167">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="2f08e-167">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="2f08e-168">Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-168">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="2f08e-169">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="2f08e-169">**Streaming**</span></span>

<span data-ttu-id="2f08e-170">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-170">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="2f08e-171">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="2f08e-171">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="2f08e-172">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="2f08e-172">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="2f08e-173">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-173">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="2f08e-174">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="2f08e-174">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="2f08e-175">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-175">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="2f08e-176">Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei (*pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):</span><span class="sxs-lookup"><span data-stu-id="2f08e-176">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="2f08e-177">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="2f08e-177">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="2f08e-178">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-178">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="2f08e-179">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-179">There's no validation.</span></span>

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

<span data-ttu-id="2f08e-180">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2f08e-180">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="2f08e-181">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="2f08e-181">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="2f08e-182">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-182">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="2f08e-183">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-183">For example:</span></span>

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

<span data-ttu-id="2f08e-184">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-184">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="2f08e-185">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-185">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="2f08e-186">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-186">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="2f08e-187">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-187">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="2f08e-188">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="2f08e-188">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="2f08e-189">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="2f08e-189">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="2f08e-190">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="2f08e-190">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="2f08e-191">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-191">Applications should:</span></span>
>
> * <span data-ttu-id="2f08e-192">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="2f08e-192">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="2f08e-193">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="2f08e-193">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="2f08e-194">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="2f08e-194">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="2f08e-195">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-195">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="2f08e-196">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-196">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="2f08e-197">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="2f08e-197">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="2f08e-198">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-198">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="2f08e-199">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-199">Validation</span></span>](#validation)

<span data-ttu-id="2f08e-200">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="2f08e-200">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="2f08e-201">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="2f08e-201">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="2f08e-202">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-202">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="2f08e-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="2f08e-203">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="2f08e-204">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-204">Binding matches form files by name.</span></span> <span data-ttu-id="2f08e-205">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="2f08e-205">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="2f08e-206">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="2f08e-206">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="2f08e-207">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-207">The following example:</span></span>

* <span data-ttu-id="2f08e-208">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-208">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="2f08e-209">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-209">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="2f08e-210">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-210">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="2f08e-211">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="2f08e-211">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="2f08e-212">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="2f08e-212">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="2f08e-213">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-213">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="2f08e-214">Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="2f08e-214">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="2f08e-215">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="2f08e-215">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="2f08e-216">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-216">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="2f08e-217">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-217">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="2f08e-218">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="2f08e-218">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="2f08e-219">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2f08e-219">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-220">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="2f08e-221">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="2f08e-221">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="2f08e-222">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-222">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="2f08e-223">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="2f08e-223">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="2f08e-224">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-224">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="2f08e-225">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="2f08e-225">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="2f08e-226">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="2f08e-226">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="2f08e-227"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-227"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="2f08e-228">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-228">The prior example uses a bound model property.</span></span>

<span data-ttu-id="2f08e-229">Die `FileUpload` wird im Seiten Format verwendet Razor :</span><span class="sxs-lookup"><span data-stu-id="2f08e-229">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="2f08e-230">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2f08e-230">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="2f08e-231">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="2f08e-231">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="2f08e-232">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="2f08e-232">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="2f08e-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2f08e-233">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="2f08e-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2f08e-234">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-235">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="2f08e-235">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="2f08e-236">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="2f08e-236">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="2f08e-237">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-237">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="2f08e-238">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-238">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="2f08e-239">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="2f08e-239">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="2f08e-240">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-240">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="2f08e-241">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-241">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="2f08e-242">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="2f08e-242">Upload large files with streaming</span></span>

<span data-ttu-id="2f08e-243">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-243">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="2f08e-244">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-244">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="2f08e-245">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-245">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="2f08e-246">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-246">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="2f08e-247">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="2f08e-247">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="2f08e-248">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="2f08e-248">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="2f08e-249">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-249">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="2f08e-250">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-250">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="2f08e-251">In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-251">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="2f08e-252">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="2f08e-252">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="2f08e-253">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-253">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="2f08e-254">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-254">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="2f08e-255">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-255">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="2f08e-256">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-256">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="2f08e-257">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="2f08e-257">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="2f08e-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="2f08e-258">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="2f08e-259">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="2f08e-259">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="2f08e-260">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-260">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="2f08e-261">Validierung</span><span class="sxs-lookup"><span data-stu-id="2f08e-261">Validation</span></span>

<span data-ttu-id="2f08e-262">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="2f08e-262">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="2f08e-263">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="2f08e-263">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="2f08e-264">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-264">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-265">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-265">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="2f08e-266">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-266">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="2f08e-267">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="2f08e-267">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="2f08e-268">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="2f08e-268">Fully understand the implementation.</span></span>
> * <span data-ttu-id="2f08e-269">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="2f08e-269">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="2f08e-270">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-270">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="2f08e-271">Inhaltsprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-271">Content validation</span></span>

<span data-ttu-id="2f08e-272">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-272">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="2f08e-273">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-273">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="2f08e-274">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-274">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="2f08e-275">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="2f08e-275">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="2f08e-276">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-276">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="2f08e-277">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-277">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="2f08e-278">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-278">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="2f08e-279">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-279">File extension validation</span></span>

<span data-ttu-id="2f08e-280">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-280">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="2f08e-281">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-281">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="2f08e-282">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="2f08e-282">File signature validation</span></span>

<span data-ttu-id="2f08e-283">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-283">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="2f08e-284">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="2f08e-284">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="2f08e-285">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-285">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="2f08e-286">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-286">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="2f08e-287">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-287">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="2f08e-288">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="2f08e-288">File name security</span></span>

<span data-ttu-id="2f08e-289">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-289">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="2f08e-290">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-290">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="2f08e-291">automatisch HTML-codiert Eigenschaftswerte für die Anzeige.</span><span class="sxs-lookup"><span data-stu-id="2f08e-291"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="2f08e-292">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2f08e-292">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="2f08e-293">Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.</span><span class="sxs-lookup"><span data-stu-id="2f08e-293">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="2f08e-294">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-294">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="2f08e-295">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-295">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="2f08e-296">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="2f08e-296">Size validation</span></span>

<span data-ttu-id="2f08e-297">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-297">Limit the size of uploaded files.</span></span>

<span data-ttu-id="2f08e-298">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="2f08e-298">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="2f08e-299">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="2f08e-299">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="2f08e-300">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-300">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="2f08e-301">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-301">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="2f08e-302">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="2f08e-302">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="2f08e-303">In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-303">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="2f08e-304">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-304">In the following example:</span></span>

* <span data-ttu-id="2f08e-305">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-305">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="2f08e-306">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-306">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="2f08e-307">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="2f08e-307">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="2f08e-308">Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-308">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="2f08e-309">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="2f08e-309">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="2f08e-310">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2f08e-310">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="2f08e-311">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="2f08e-311">Multipart body length limit</span></span>

<span data-ttu-id="2f08e-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="2f08e-312"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="2f08e-313">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="2f08e-313">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="2f08e-314">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="2f08e-314">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="2f08e-315">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-315">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="2f08e-316"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="2f08e-317">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-317">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-318">RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-318">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="2f08e-319">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="2f08e-319">Kestrel maximum request body size</span></span>

<span data-ttu-id="2f08e-320">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2f08e-320">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="2f08e-321">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-321">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="2f08e-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="2f08e-322"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="2f08e-323">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-323">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-324">RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-324">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="2f08e-325">`RequestSizeLimitAttribute`Kann auch mit der- [`@attribute`](xref:mvc/views/razor#attribute) Razor Direktive angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="2f08e-325">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="2f08e-326">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="2f08e-326">Other Kestrel limits</span></span>

<span data-ttu-id="2f08e-327">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="2f08e-327">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="2f08e-328">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-328">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="2f08e-329">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="2f08e-329">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="2f08e-330">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="2f08e-330">IIS content length limit</span></span>

<span data-ttu-id="2f08e-331">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2f08e-331">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="2f08e-332">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-332">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="2f08e-333">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="2f08e-333">This setting only applies to IIS.</span></span> <span data-ttu-id="2f08e-334">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="2f08e-334">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="2f08e-335">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="2f08e-335">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="2f08e-336">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="2f08e-336">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="2f08e-337">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711) (Upload von Dateien größer als 2 GB ist nicht möglich).</span><span class="sxs-lookup"><span data-stu-id="2f08e-337">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2f08e-338">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="2f08e-338">Troubleshoot</span></span>

<span data-ttu-id="2f08e-339">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="2f08e-339">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="2f08e-340">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="2f08e-340">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="2f08e-341">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-341">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="2f08e-342">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="2f08e-342">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="2f08e-343">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="2f08e-343">Connection failure</span></span>

<span data-ttu-id="2f08e-344">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-344">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="2f08e-345">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="2f08e-345">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="2f08e-346">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-346">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="2f08e-347">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="2f08e-347">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="2f08e-348">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-348">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="2f08e-349">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-349">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="2f08e-350">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-350">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="2f08e-351">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="2f08e-351">Stream was too long</span></span>

<span data-ttu-id="2f08e-352">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="2f08e-352">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="2f08e-353">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-353">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="2f08e-354">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-354">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f08e-355">ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-355">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="2f08e-356">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f08e-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="2f08e-357">Sicherheitshinweise</span><span class="sxs-lookup"><span data-stu-id="2f08e-357">Security considerations</span></span>

<span data-ttu-id="2f08e-358">Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-358">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="2f08e-359">Angreifer versuchen möglicherweise Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2f08e-359">Attackers may attempt to:</span></span>

* <span data-ttu-id="2f08e-360">Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen</span><span class="sxs-lookup"><span data-stu-id="2f08e-360">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="2f08e-361">Hochladen von Viren oder Schadsoftware</span><span class="sxs-lookup"><span data-stu-id="2f08e-361">Upload viruses or malware.</span></span>
* <span data-ttu-id="2f08e-362">Gefährden von Netzwerken und Servern auf andere Weise</span><span class="sxs-lookup"><span data-stu-id="2f08e-362">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="2f08e-363">Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:</span><span class="sxs-lookup"><span data-stu-id="2f08e-363">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="2f08e-364">Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk.</span><span class="sxs-lookup"><span data-stu-id="2f08e-364">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="2f08e-365">Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-365">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="2f08e-366">Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-366">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="2f08e-367">Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-367">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="2f08e-368">Wählen Sie einen sicheren von der App festgelegten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-368">Use a safe file name determined by the app.</span></span> <span data-ttu-id="2f08e-369">Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-369">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="2f08e-370">Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).</span><span class="sxs-lookup"><span data-stu-id="2f08e-370">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="2f08e-371">Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-371">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="2f08e-372">Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-372">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="2f08e-373">Überprüfen Sie die Größe einer hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-373">Check the size of an uploaded file.</span></span> <span data-ttu-id="2f08e-374">Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;</span><span class="sxs-lookup"><span data-stu-id="2f08e-374">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="2f08e-375">Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-375">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="2f08e-376">**Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-376">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="2f08e-377">&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-377">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-378">Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-378">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="2f08e-379">Erlangen der vollständigen Kontrolle über ein System.</span><span class="sxs-lookup"><span data-stu-id="2f08e-379">Completely gain control of a system.</span></span>
> * <span data-ttu-id="2f08e-380">Überlasten eines Systems mit dem Ziel eines Systemausfalls.</span><span class="sxs-lookup"><span data-stu-id="2f08e-380">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="2f08e-381">Kompromittieren von Benutzer- oder Systemdaten</span><span class="sxs-lookup"><span data-stu-id="2f08e-381">Compromise user or system data.</span></span>
> * <span data-ttu-id="2f08e-382">Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="2f08e-382">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="2f08e-383">Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="2f08e-383">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="2f08e-384">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="2f08e-384">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="2f08e-385">Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden</span><span class="sxs-lookup"><span data-stu-id="2f08e-385">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="2f08e-386">Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).</span><span class="sxs-lookup"><span data-stu-id="2f08e-386">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="2f08e-387">Speicherszenarien</span><span class="sxs-lookup"><span data-stu-id="2f08e-387">Storage scenarios</span></span>

<span data-ttu-id="2f08e-388">Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="2f08e-388">Common storage options for files include:</span></span>

* <span data-ttu-id="2f08e-389">Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-389">Database</span></span>

  * <span data-ttu-id="2f08e-390">Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).</span><span class="sxs-lookup"><span data-stu-id="2f08e-390">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="2f08e-391">Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="2f08e-391">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="2f08e-392">Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="2f08e-392">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="2f08e-393">Physischer Speicher (Dateisystem oder Netzwerkfreigabe)</span><span class="sxs-lookup"><span data-stu-id="2f08e-393">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="2f08e-394">Für das Hochladen großer Dateien:</span><span class="sxs-lookup"><span data-stu-id="2f08e-394">For large file uploads:</span></span>
    * <span data-ttu-id="2f08e-395">Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.</span><span class="sxs-lookup"><span data-stu-id="2f08e-395">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="2f08e-396">Physischer Speicher ist oft teurer als Datenbankspeicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-396">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="2f08e-397">Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.</span><span class="sxs-lookup"><span data-stu-id="2f08e-397">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="2f08e-398">Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-398">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="2f08e-399">**Erteilen Sie niemals die Ausführungsberechtigung.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-399">**Never grant execute permission.**</span></span>

* <span data-ttu-id="2f08e-400">Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="2f08e-400">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="2f08e-401">Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-401">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="2f08e-402">Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.</span><span class="sxs-lookup"><span data-stu-id="2f08e-402">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="2f08e-403">Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="2f08e-403">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="2f08e-404">Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-404">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="2f08e-405">Szenarien für das Hochladen von Dateien</span><span class="sxs-lookup"><span data-stu-id="2f08e-405">File upload scenarios</span></span>

<span data-ttu-id="2f08e-406">Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.</span><span class="sxs-lookup"><span data-stu-id="2f08e-406">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="2f08e-407">**Pufferung**</span><span class="sxs-lookup"><span data-stu-id="2f08e-407">**Buffering**</span></span>

<span data-ttu-id="2f08e-408">Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-408">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="2f08e-409">Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig.</span><span class="sxs-lookup"><span data-stu-id="2f08e-409">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="2f08e-410">Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="2f08e-410">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="2f08e-411">Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.</span><span class="sxs-lookup"><span data-stu-id="2f08e-411">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="2f08e-412">Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-412">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="2f08e-413">Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-413">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="2f08e-414">Physischer Speicher</span><span class="sxs-lookup"><span data-stu-id="2f08e-414">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="2f08e-415">Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-415">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="2f08e-416">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="2f08e-416">**Streaming**</span></span>

<span data-ttu-id="2f08e-417">Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-417">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="2f08e-418">Streaming verbessert die Leistung nicht wesentlich.</span><span class="sxs-lookup"><span data-stu-id="2f08e-418">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="2f08e-419">Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="2f08e-419">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="2f08e-420">Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-420">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="2f08e-421">Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher</span><span class="sxs-lookup"><span data-stu-id="2f08e-421">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="2f08e-422">Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-422">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="2f08e-423">Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei (*pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):</span><span class="sxs-lookup"><span data-stu-id="2f08e-423">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="2f08e-424">Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:</span><span class="sxs-lookup"><span data-stu-id="2f08e-424">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="2f08e-425">Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-425">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="2f08e-426">Keine Validierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-426">There's no validation.</span></span>

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

<span data-ttu-id="2f08e-427">Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="2f08e-427">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="2f08e-428">Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="2f08e-428">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="2f08e-429">Verwenden Sie `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-429">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="2f08e-430">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-430">For example:</span></span>

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

<span data-ttu-id="2f08e-431">HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-431">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="2f08e-432">Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-432">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="2f08e-433">Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-433">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="2f08e-434">Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-434">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="2f08e-435">Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="2f08e-435">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="2f08e-436">Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML.</span><span class="sxs-lookup"><span data-stu-id="2f08e-436">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="2f08e-437">Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade.</span><span class="sxs-lookup"><span data-stu-id="2f08e-437">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="2f08e-438">Anwendungen sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-438">Applications should:</span></span>
>
> * <span data-ttu-id="2f08e-439">den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen</span><span class="sxs-lookup"><span data-stu-id="2f08e-439">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="2f08e-440">den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern</span><span class="sxs-lookup"><span data-stu-id="2f08e-440">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="2f08e-441">einen neuen zufälligen Dateinamen für die Speicherung generieren</span><span class="sxs-lookup"><span data-stu-id="2f08e-441">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="2f08e-442">Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-442">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="2f08e-443">Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-443">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="2f08e-444">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="2f08e-444">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="2f08e-445">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-445">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="2f08e-446">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-446">Validation</span></span>](#validation)

<span data-ttu-id="2f08e-447">Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="2f08e-447">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="2f08e-448">Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="2f08e-448">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="2f08e-449">Eine der folgenden Sammlungen, die mehrere Dateien darstellen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-449">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="2f08e-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="2f08e-450">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="2f08e-451">Zur Bindung werden Formulardateien anhand des Namens abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-451">Binding matches form files by name.</span></span> <span data-ttu-id="2f08e-452">So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`).</span><span class="sxs-lookup"><span data-stu-id="2f08e-452">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="2f08e-453">Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="2f08e-453">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="2f08e-454">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-454">The following example:</span></span>

* <span data-ttu-id="2f08e-455">Durchläuft mindestens eine hochgeladene Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-455">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="2f08e-456">Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-456">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="2f08e-457">Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-457">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="2f08e-458">Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.</span><span class="sxs-lookup"><span data-stu-id="2f08e-458">Returns the total number and size of files uploaded.</span></span>

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

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="2f08e-459">Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren.</span><span class="sxs-lookup"><span data-stu-id="2f08e-459">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="2f08e-460">Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-460">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="2f08e-461">Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="2f08e-461">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="2f08e-462">Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="2f08e-462">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="2f08e-463">Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-463">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="2f08e-464">Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-464">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="2f08e-465">Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="2f08e-465">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="2f08e-466">Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2f08e-466">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-467">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="2f08e-468">Der Grenzwert von 65.535 Dateien gilt pro Server.</span><span class="sxs-lookup"><span data-stu-id="2f08e-468">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="2f08e-469">Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-469">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="2f08e-470">GetTempFileNameA-Funktion</span><span class="sxs-lookup"><span data-stu-id="2f08e-470">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="2f08e-471">Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank</span><span class="sxs-lookup"><span data-stu-id="2f08e-471">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="2f08e-472">Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:</span><span class="sxs-lookup"><span data-stu-id="2f08e-472">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="2f08e-473">Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:</span><span class="sxs-lookup"><span data-stu-id="2f08e-473">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="2f08e-474"><xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-474"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="2f08e-475">Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-475">The prior example uses a bound model property.</span></span>

<span data-ttu-id="2f08e-476">Die `FileUpload` wird im Seiten Format verwendet Razor :</span><span class="sxs-lookup"><span data-stu-id="2f08e-476">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="2f08e-477">Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2f08e-477">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="2f08e-478">Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:</span><span class="sxs-lookup"><span data-stu-id="2f08e-478">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="2f08e-479">Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:</span><span class="sxs-lookup"><span data-stu-id="2f08e-479">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="2f08e-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="2f08e-480">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="2f08e-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="2f08e-481">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-482">Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.</span><span class="sxs-lookup"><span data-stu-id="2f08e-482">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="2f08e-483">Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht.</span><span class="sxs-lookup"><span data-stu-id="2f08e-483">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="2f08e-484">Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-484">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="2f08e-485">Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-485">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="2f08e-486">Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span><span class="sxs-lookup"><span data-stu-id="2f08e-486">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="2f08e-487">Sicherheitsüberlegungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-487">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="2f08e-488">Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-488">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="2f08e-489">Hochladen von großen Dateien mittels Streaming</span><span class="sxs-lookup"><span data-stu-id="2f08e-489">Upload large files with streaming</span></span>

<span data-ttu-id="2f08e-490">Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-490">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="2f08e-491">Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-491">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="2f08e-492">Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-492">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="2f08e-493">Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-493">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="2f08e-494">Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.</span><span class="sxs-lookup"><span data-stu-id="2f08e-494">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="2f08e-495">Die Reaktion auf der ersten Seite lädt das Formular und speichert das Fälschungssicherheitstoken (über das `GenerateAntiforgeryTokenCookieAttribute`-Attribut) in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="2f08e-495">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="2f08e-496">Das Attribut nutzt die in ASP.NET Core integrierte [Unterstützung der Fälschungssicherheit](xref:security/anti-request-forgery), um ein Cookie mit einem Anforderungstoken festzulegen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-496">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="2f08e-497">Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-497">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="2f08e-498">In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-498">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="2f08e-499">Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin).</span><span class="sxs-lookup"><span data-stu-id="2f08e-499">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="2f08e-500">Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-500">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="2f08e-501">Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-501">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="2f08e-502">Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-502">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="2f08e-503">Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-503">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="2f08e-504">Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:</span><span class="sxs-lookup"><span data-stu-id="2f08e-504">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="2f08e-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="2f08e-505">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="2f08e-506">Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:</span><span class="sxs-lookup"><span data-stu-id="2f08e-506">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="2f08e-507">In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-507">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="2f08e-508">Validierung</span><span class="sxs-lookup"><span data-stu-id="2f08e-508">Validation</span></span>

<span data-ttu-id="2f08e-509">Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="2f08e-509">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="2f08e-510">Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode.</span><span class="sxs-lookup"><span data-stu-id="2f08e-510">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="2f08e-511">Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.</span><span class="sxs-lookup"><span data-stu-id="2f08e-511">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="2f08e-512">Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-512">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="2f08e-513">In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-513">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="2f08e-514">Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:</span><span class="sxs-lookup"><span data-stu-id="2f08e-514">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="2f08e-515">Sie verstehen die Implementierung vollständig.</span><span class="sxs-lookup"><span data-stu-id="2f08e-515">Fully understand the implementation.</span></span>
> * <span data-ttu-id="2f08e-516">Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.</span><span class="sxs-lookup"><span data-stu-id="2f08e-516">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="2f08e-517">**Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-517">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="2f08e-518">Inhaltsprüfung</span><span class="sxs-lookup"><span data-stu-id="2f08e-518">Content validation</span></span>

<span data-ttu-id="2f08e-519">**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**</span><span class="sxs-lookup"><span data-stu-id="2f08e-519">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="2f08e-520">Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-520">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="2f08e-521">Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2f08e-521">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="2f08e-522">Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft.</span><span class="sxs-lookup"><span data-stu-id="2f08e-522">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="2f08e-523">Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-523">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="2f08e-524">Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-524">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="2f08e-525">Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-525">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="2f08e-526">Validierung von Dateierweiterungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-526">File extension validation</span></span>

<span data-ttu-id="2f08e-527">Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-527">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="2f08e-528">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-528">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="2f08e-529">Validierung der Dateisignatur</span><span class="sxs-lookup"><span data-stu-id="2f08e-529">File signature validation</span></span>

<span data-ttu-id="2f08e-530">Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-530">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="2f08e-531">Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht.</span><span class="sxs-lookup"><span data-stu-id="2f08e-531">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="2f08e-532">Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-532">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="2f08e-533">Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:</span><span class="sxs-lookup"><span data-stu-id="2f08e-533">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="2f08e-534">Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-534">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="2f08e-535">Sicherheit von Dateinamen</span><span class="sxs-lookup"><span data-stu-id="2f08e-535">File name security</span></span>

<span data-ttu-id="2f08e-536">Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher.</span><span class="sxs-lookup"><span data-stu-id="2f08e-536">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="2f08e-537">Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-537">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

Razor<span data-ttu-id="2f08e-538">automatisch HTML-codiert Eigenschaftswerte für die Anzeige.</span><span class="sxs-lookup"><span data-stu-id="2f08e-538"> automatically HTML encodes property values for display.</span></span> <span data-ttu-id="2f08e-539">Der folgende Code kann sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2f08e-539">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="2f08e-540">Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.</span><span class="sxs-lookup"><span data-stu-id="2f08e-540">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="2f08e-541">Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben.</span><span class="sxs-lookup"><span data-stu-id="2f08e-541">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="2f08e-542">Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-542">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="2f08e-543">Validierung der Größe</span><span class="sxs-lookup"><span data-stu-id="2f08e-543">Size validation</span></span>

<span data-ttu-id="2f08e-544">Begrenzen Sie die Größe hochgeladener Dateien.</span><span class="sxs-lookup"><span data-stu-id="2f08e-544">Limit the size of uploaded files.</span></span>

<span data-ttu-id="2f08e-545">In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes).</span><span class="sxs-lookup"><span data-stu-id="2f08e-545">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="2f08e-546">Der Grenzwert wird mittels [Konfiguration](xref:fundamentals/configuration/index) in der Datei *appsettings.json* angegeben:</span><span class="sxs-lookup"><span data-stu-id="2f08e-546">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="2f08e-547">`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-547">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="2f08e-548">Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-548">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="2f08e-549">Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode</span><span class="sxs-lookup"><span data-stu-id="2f08e-549">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="2f08e-550">In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.</span><span class="sxs-lookup"><span data-stu-id="2f08e-550">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="2f08e-551">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2f08e-551">In the following example:</span></span>

* <span data-ttu-id="2f08e-552">Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-552">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="2f08e-553">Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="2f08e-553">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="2f08e-554">Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):</span><span class="sxs-lookup"><span data-stu-id="2f08e-554">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="2f08e-555">Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-555">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="2f08e-556">Für eine MVC POST-Controlleraktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="2f08e-556">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="2f08e-557">Server- und App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2f08e-557">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="2f08e-558">Grenzwert der Länge von mehrteiligem Text</span><span class="sxs-lookup"><span data-stu-id="2f08e-558">Multipart body length limit</span></span>

<span data-ttu-id="2f08e-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest.</span><span class="sxs-lookup"><span data-stu-id="2f08e-559"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="2f08e-560">Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus.</span><span class="sxs-lookup"><span data-stu-id="2f08e-560">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="2f08e-561">Der Standardwert ist 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="2f08e-561">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="2f08e-562">Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-562">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="2f08e-563"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="2f08e-564">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-564">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-565">RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-565">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="2f08e-566">Maximale Größe des Anforderungstexts für Kestrel</span><span class="sxs-lookup"><span data-stu-id="2f08e-566">Kestrel maximum request body size</span></span>

<span data-ttu-id="2f08e-567">Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2f08e-567">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="2f08e-568">Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-568">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="2f08e-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="2f08e-569"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="2f08e-570">Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2f08e-570">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="2f08e-571">RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-571">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="2f08e-572">Andere Kestrel-Grenzwerte</span><span class="sxs-lookup"><span data-stu-id="2f08e-572">Other Kestrel limits</span></span>

<span data-ttu-id="2f08e-573">Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:</span><span class="sxs-lookup"><span data-stu-id="2f08e-573">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="2f08e-574">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-574">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="2f08e-575">Datenraten für Anforderungen und Antworten</span><span class="sxs-lookup"><span data-stu-id="2f08e-575">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="2f08e-576">Grenzwert der Länge von IIS-Inhalten</span><span class="sxs-lookup"><span data-stu-id="2f08e-576">IIS content length limit</span></span>

<span data-ttu-id="2f08e-577">Der Standardgrenzwert für Anforderungen (`maxAllowedContentLength`) beträgt standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2f08e-577">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="2f08e-578">Passen Sie den Grenzwert in der Datei *web.config* an:</span><span class="sxs-lookup"><span data-stu-id="2f08e-578">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="2f08e-579">Diese Einstellung gilt nur für IIS.</span><span class="sxs-lookup"><span data-stu-id="2f08e-579">This setting only applies to IIS.</span></span> <span data-ttu-id="2f08e-580">Beim Hosting unter Kestrel gehört dieses Verhalten nicht zum Standard.</span><span class="sxs-lookup"><span data-stu-id="2f08e-580">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="2f08e-581">Weitere Informationen finden Sie unter [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/) (Anforderungseinschränkungen).</span><span class="sxs-lookup"><span data-stu-id="2f08e-581">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="2f08e-582">Einschränkungen im ASP.NET Core-Modul oder das Vorhandensein des IIS-Anforderungsfiltermoduls können Uploads auf entweder 2 oder 4 GB beschränken.</span><span class="sxs-lookup"><span data-stu-id="2f08e-582">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="2f08e-583">Weitere Informationen finden Sie unter [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711) (Upload von Dateien größer als 2 GB ist nicht möglich).</span><span class="sxs-lookup"><span data-stu-id="2f08e-583">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2f08e-584">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="2f08e-584">Troubleshoot</span></span>

<span data-ttu-id="2f08e-585">Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="2f08e-585">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="2f08e-586">Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server</span><span class="sxs-lookup"><span data-stu-id="2f08e-586">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="2f08e-587">Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:</span><span class="sxs-lookup"><span data-stu-id="2f08e-587">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="2f08e-588">Weitere Informationen zur Erhöhung des Grenzwerts finden Sie im Abschnitt [Grenzwert der Länge von IIS-Inhalten](#iis-content-length-limit).</span><span class="sxs-lookup"><span data-stu-id="2f08e-588">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="2f08e-589">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="2f08e-589">Connection failure</span></span>

<span data-ttu-id="2f08e-590">Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet.</span><span class="sxs-lookup"><span data-stu-id="2f08e-590">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="2f08e-591">Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size).</span><span class="sxs-lookup"><span data-stu-id="2f08e-591">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="2f08e-592">Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="2f08e-592">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="2f08e-593">Ausnahme bei möglichem NULL-Verweis mit IFormFile</span><span class="sxs-lookup"><span data-stu-id="2f08e-593">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="2f08e-594">Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-594">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="2f08e-595">Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-595">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="2f08e-596">Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2f08e-596">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="2f08e-597">Stream war zu lang.</span><span class="sxs-lookup"><span data-stu-id="2f08e-597">Stream was too long</span></span>

<span data-ttu-id="2f08e-598">Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="2f08e-598">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="2f08e-599">Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="2f08e-599">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="2f08e-600">Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.</span><span class="sxs-lookup"><span data-stu-id="2f08e-600">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="2f08e-601">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2f08e-601">Additional resources</span></span>

* [<span data-ttu-id="2f08e-602">HTTP-Verbindungs Anforderungs Ableitung</span><span class="sxs-lookup"><span data-stu-id="2f08e-602">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="2f08e-603">Unrestricted File Upload (Uneingeschränkter Dateiupload)</span><span class="sxs-lookup"><span data-stu-id="2f08e-603">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="2f08e-604">Azure-Sicherheit: Sicherheitsrahmen: Eingabevalidierung | Entschärfungen</span><span class="sxs-lookup"><span data-stu-id="2f08e-604">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="2f08e-605">Azure Cloud Design Patterns: Valet-Schlüssel Muster</span><span class="sxs-lookup"><span data-stu-id="2f08e-605">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
