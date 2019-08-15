---
title: Testen von Web-APIs mit HTTP REPL
author: scottaddie
description: Hier erfahren Sie, wie Sie das globale .NET Core-Tool HTTP REPL verwenden können, um ASP.NET Core-Web-APIs zu durchsuchen und zu testen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: 0e80fcd76a4d3efcd35140c52e0f6f0ae0f27932
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862963"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="e23c0-103">Testen von Web-APIs mit HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="e23c0-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="e23c0-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e23c0-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e23c0-105">HTTP REPL (read–eval–print-Loop):</span><span class="sxs-lookup"><span data-stu-id="e23c0-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="e23c0-106">Ein schlankes, plattformübergreifendes Befehlszeilentool, das überall unterstützt wird, wo .NET Core unterstützt wird</span><span class="sxs-lookup"><span data-stu-id="e23c0-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="e23c0-107">wird zum Senden von HTTP-Anforderungen verwendet, mit denen ASP.NET Core-Web-APIs (und andere APIs) getestet werden können, und zum Überprüfen von deren Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="e23c0-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="e23c0-108">kann zum Testen von Web-APIs verwendet werden, die in beliebigen Umgebungen gehostet werden (einschließlich Localhost und Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="e23c0-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="e23c0-109">Folgende [HTTP-Verben](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="e23c0-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="e23c0-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="e23c0-111">GET</span><span class="sxs-lookup"><span data-stu-id="e23c0-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="e23c0-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="e23c0-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="e23c0-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="e23c0-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="e23c0-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="e23c0-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="e23c0-115">POST</span><span class="sxs-lookup"><span data-stu-id="e23c0-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="e23c0-116">PUT</span><span class="sxs-lookup"><span data-stu-id="e23c0-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="e23c0-117">[Sehen Sie sich die beispielhafte ASP.NET Core-Web-API an, oder laden Sie sie herunter ([Downloadanleitung](xref:index#how-to-download-a-sample))](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples), um dem Artikel zu folgen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e23c0-118">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="e23c0-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="e23c0-119">Installation</span><span class="sxs-lookup"><span data-stu-id="e23c0-119">Installation</span></span>

<span data-ttu-id="e23c0-120">Führen Sie den folgenden Befehl aus, um HTTP REPL zu installieren:</span><span class="sxs-lookup"><span data-stu-id="e23c0-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="e23c0-121">Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) installiert.</span><span class="sxs-lookup"><span data-stu-id="e23c0-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="e23c0-122">Verwendung</span><span class="sxs-lookup"><span data-stu-id="e23c0-122">Usage</span></span>

<span data-ttu-id="e23c0-123">Nach der erfolgreichen Installation des Tools können Sie folgenden Befehl ausführen, um HTTP REPL zu starten:</span><span class="sxs-lookup"><span data-stu-id="e23c0-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="e23c0-124">Führen Sie einen der folgenden Befehle aus, um eine Liste der verfügbaren HTTP REPL-Befehle anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="e23c0-125">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-125">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Sets the swagger document to use for information about the current server
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="e23c0-126">In HTTP REPL können Befehle vervollständigt werden.</span><span class="sxs-lookup"><span data-stu-id="e23c0-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="e23c0-127">Wenn Sie die <kbd>TAB-TASTE</kbd> drücken, können Sie die Liste der Befehle durchlaufen, die die eingegebenen Zeichen oder API-Endpunkte vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="e23c0-128">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="e23c0-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="e23c0-129">Herstellen einer Verbindung mit der Web-API</span><span class="sxs-lookup"><span data-stu-id="e23c0-129">Connect to the web API</span></span>

<span data-ttu-id="e23c0-130">Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit einer Web-API her:</span><span class="sxs-lookup"><span data-stu-id="e23c0-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="e23c0-131">`<BASE URI>` ist der Basis-URI für die Web-API.</span><span class="sxs-lookup"><span data-stu-id="e23c0-131">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="e23c0-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="e23c0-133">Alternativ können Sie den folgenden Befehl jederzeit ausführen, während HTTP REPL ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="e23c0-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="e23c0-134">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-134">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="e23c0-135">Verweis auf ein Swagger-Dokument für die Web-API</span><span class="sxs-lookup"><span data-stu-id="e23c0-135">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="e23c0-136">Legen Sie den relativen URI auf das Swagger-Dokument für die Web-API fest, um diese ordnungsgemäß zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-136">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="e23c0-137">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-137">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="e23c0-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-138">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="e23c0-139">Navigieren in der Web-API</span><span class="sxs-lookup"><span data-stu-id="e23c0-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="e23c0-140">Anzeigen verfügbarer Endpunkte</span><span class="sxs-lookup"><span data-stu-id="e23c0-140">View available endpoints</span></span>

<span data-ttu-id="e23c0-141">Führen Sie den Befehl `ls` oder `dir` aus, um die unterschiedlichen Endpunkte (Controller) im aktuellen Pfad der Web-API-Adresse aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="e23c0-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="e23c0-142">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="e23c0-143">Die vorherige Ausgabe gibt an, dass die zwei Controller `Fruits` und `People` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="e23c0-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="e23c0-144">Beide Controller unterstützen parameterlose HTTP GET- und HTTP POST-Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="e23c0-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="e23c0-145">Wenn Sie zu einem bestimmten Controller navigieren, werden weitere Details angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="e23c0-146">Aus der Ausgabe des folgenden Befehls geht beispielsweise hervor, dass der Controller `Fruits` auch HTTP GET-, HTTP PUT- und HTTP DELETE-Vorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="e23c0-147">Jeder dieser Vorgänge erwartet einen `id`-Parameter in der Route:</span><span class="sxs-lookup"><span data-stu-id="e23c0-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="e23c0-148">Führen Sie alternativ den Befehl `ui` aus, um die Swagger-Benutzeroberflächenseite der Web-API in einem Browser zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="e23c0-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="e23c0-150">Navigieren zu einem Endpunkt</span><span class="sxs-lookup"><span data-stu-id="e23c0-150">Navigate to an endpoint</span></span>

<span data-ttu-id="e23c0-151">Führen Sie den Befehl `cd` aus, um zu einem anderen Endpunkt in der Web-API zu navigieren:</span><span class="sxs-lookup"><span data-stu-id="e23c0-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="e23c0-152">Für den Pfad im Befehl `cd` wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="e23c0-153">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="e23c0-154">Anpassen von HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="e23c0-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="e23c0-155">Die [Standardfarben](#set-color-preferences) von HTTP REPL können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="e23c0-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="e23c0-156">Außerdem kann definiert werden, welcher [Text-Editor](#set-the-default-text-editor) standardmäßig verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="e23c0-157">Die HTTP REPL-Einstellungen werden in der aktuellen Sitzung beibehalten und in zukünftigen Sitzungen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="e23c0-158">Nach der Änderung werden die Einstellungen in der folgenden Datei gespeichert:</span><span class="sxs-lookup"><span data-stu-id="e23c0-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="e23c0-159">Linux</span><span class="sxs-lookup"><span data-stu-id="e23c0-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="e23c0-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e23c0-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="e23c0-161">macOS</span><span class="sxs-lookup"><span data-stu-id="e23c0-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="e23c0-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e23c0-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="e23c0-163">Windows</span><span class="sxs-lookup"><span data-stu-id="e23c0-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="e23c0-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="e23c0-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="e23c0-165">Die *HTTPREPLPREFS-Datei* wird beim Start geladen und bei der Laufzeit nicht auf Änderungen überwacht.</span><span class="sxs-lookup"><span data-stu-id="e23c0-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="e23c0-166">Manuelle Änderungen an der Datei werden erst nach dem Neustart des Tools wirksam.</span><span class="sxs-lookup"><span data-stu-id="e23c0-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="e23c0-167">Anzeigen der Einstellungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-167">View the settings</span></span>

<span data-ttu-id="e23c0-168">Führen Sie den Befehl `pref get` aus, um die verfügbaren Einstellungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="e23c0-169">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="e23c0-170">Mit dem vorangehenden Befehl werden die verfügbaren Schlüssel-Wert-Paare angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="e23c0-171">Festlegen von Farbeinstellungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-171">Set color preferences</span></span>

<span data-ttu-id="e23c0-172">Die farbliche Kennzeichnung von Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="e23c0-173">Sie können die Standardfarben des HTTP REPL-Tools anpassen, indem Sie den Schlüssel für die entsprechende Farbe suchen und ändern.</span><span class="sxs-lookup"><span data-stu-id="e23c0-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="e23c0-174">Weitere Informationen zum Suchen von Schlüsseln finden Sie im Abschnitt [Anzeigen der Einstellungen](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="e23c0-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="e23c0-175">Ändern Sie beispielsweise den Wert des Schlüssels `colors.json` folgendermaßen von `Green` in `White`:</span><span class="sxs-lookup"><span data-stu-id="e23c0-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="e23c0-176">Es können nur die [zulässigen Farben](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e23c0-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="e23c0-177">Nachfolgende HTTP-Anforderungen zeigen die Ausgabe mit der neuen Farbgebung an.</span><span class="sxs-lookup"><span data-stu-id="e23c0-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="e23c0-178">Wenn bestimmte Farbtasten nicht festgelegt sind, werden mehr generische Schlüssel berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="e23c0-179">Anhand des folgenden Beispiels wird das Fallbackverhalten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="e23c0-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="e23c0-180">Wenn `colors.json.name` keinen Wert aufweist, wird `colors.json.string` verwendet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="e23c0-181">Wenn `colors.json.string` keinen Wert aufweist, wird `colors.json.literal` verwendet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="e23c0-182">Wenn `colors.json.literal` keinen Wert aufweist, wird `colors.json` verwendet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="e23c0-183">Wenn `colors.json` keinen Wert aufweist, wird die Standardtextfarbe (`AllowedColors.None`) der Befehlsshell verwendet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="e23c0-184">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="e23c0-184">Set indentation size</span></span>

<span data-ttu-id="e23c0-185">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="e23c0-186">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-186">The default size is two spaces.</span></span> <span data-ttu-id="e23c0-187">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="e23c0-188">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e23c0-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="e23c0-189">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="e23c0-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="e23c0-190">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-indentation-size"></a><span data-ttu-id="e23c0-191">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="e23c0-191">Set indentation size</span></span>

<span data-ttu-id="e23c0-192">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="e23c0-193">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-193">The default size is two spaces.</span></span> <span data-ttu-id="e23c0-194">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-194">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="e23c0-195">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e23c0-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="e23c0-196">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="e23c0-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="e23c0-197">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-197">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="e23c0-198">Festlegen des Standard-Text-Editors</span><span class="sxs-lookup"><span data-stu-id="e23c0-198">Set the default text editor</span></span>

<span data-ttu-id="e23c0-199">Standardmäßig ist für HTTP REPL kein Text-Editor konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e23c0-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="e23c0-200">Wenn Sie Web-API-Methoden testen möchten, für die ein HTTP-Anforderungstext erforderlich ist, müssen Sie einen Standard-Text-Editor festlegen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="e23c0-201">Das HTTP REPL-Tool startet den konfigurierten Text-Editor nur zum Verfassen des Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="e23c0-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="e23c0-202">Führen Sie den folgenden Befehl aus, um Ihren bevorzugten Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="e23c0-203">Im vorherigen Befehl ist `<EXECUTABLE>` der vollständige Pfad zur ausführbaren Datei des Text-Editors.</span><span class="sxs-lookup"><span data-stu-id="e23c0-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="e23c0-204">Führen Sie beispielsweise folgenden Befehl aus, um Visual Studio Code als Standard-Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="e23c0-205">Linux</span><span class="sxs-lookup"><span data-stu-id="e23c0-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="e23c0-206">macOS</span><span class="sxs-lookup"><span data-stu-id="e23c0-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="e23c0-207">Windows</span><span class="sxs-lookup"><span data-stu-id="e23c0-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="e23c0-208">Legen Sie den Schlüssel `editor.command.default.arguments` fest, um den Standard-Text-Editor mit bestimmten CLI-Argumenten zu starten.</span><span class="sxs-lookup"><span data-stu-id="e23c0-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="e23c0-209">Nehmen Sie beispielsweise an, dass Visual Studio Code der Standard-Text-Editor ist und Sie möchten, dass Visual Studio Code durch HTTP REPL immer in einer neuen Sitzung mit deaktivierten Erweiterungen öffnet.</span><span class="sxs-lookup"><span data-stu-id="e23c0-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="e23c0-210">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="e23c0-211">Testen von HTTP GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-211">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-212">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-212">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-213">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-213">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-214">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-214">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-215">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-215">Options</span></span>

<span data-ttu-id="e23c0-216">Für den Befehl `get` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="e23c0-216">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="e23c0-217">Beispiel</span><span class="sxs-lookup"><span data-stu-id="e23c0-217">Example</span></span>

<span data-ttu-id="e23c0-218">So führen Sie eine HTTP GET-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-218">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="e23c0-219">Führen Sie den Befehl `get` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-219">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="e23c0-220">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-220">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="e23c0-221">Rufen Sie einen bestimmten Datensatz ab, indem Sie einen Parameter an den Befehl `get` übergeben:</span><span class="sxs-lookup"><span data-stu-id="e23c0-221">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="e23c0-222">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-222">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="e23c0-223">Testen von HTTP POST-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-223">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-224">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-224">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-225">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-225">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-226">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-226">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-227">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-227">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="e23c0-228">Beispiel</span><span class="sxs-lookup"><span data-stu-id="e23c0-228">Example</span></span>

<span data-ttu-id="e23c0-229">So führen Sie eine HTTP POST-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-229">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="e23c0-230">Führen Sie den Befehl `post` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-230">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="e23c0-231">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-231">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="e23c0-232">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-232">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="e23c0-233">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-233">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="e23c0-234">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="e23c0-234">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="e23c0-235">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="e23c0-235">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="e23c0-236">Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="e23c0-236">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="e23c0-237">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-237">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="e23c0-238">Testen von HTTP PUT-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-238">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-239">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-239">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-240">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-240">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-241">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-241">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-242">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-242">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="e23c0-243">Beispiel</span><span class="sxs-lookup"><span data-stu-id="e23c0-243">Example</span></span>

<span data-ttu-id="e23c0-244">So führen Sie eine HTTP PUT-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-244">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="e23c0-245">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-245">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="e23c0-246">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-246">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="e23c0-247">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-247">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="e23c0-248">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-248">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="e23c0-249">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="e23c0-249">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="e23c0-250">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="e23c0-250">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="e23c0-251">Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="e23c0-251">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="e23c0-252">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-252">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="e23c0-253">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-253">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="e23c0-254">Wenn Sie beispielsweise „Cherry“ in den Text-Editor eingeben, gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="e23c0-254">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="e23c0-255">Testen von HTTP DELETE-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-255">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-256">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-256">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-257">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-258">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-259">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="e23c0-260">Beispiel</span><span class="sxs-lookup"><span data-stu-id="e23c0-260">Example</span></span>

<span data-ttu-id="e23c0-261">So führen Sie eine HTTP DELETE-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="e23c0-261">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="e23c0-262">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="e23c0-263">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-263">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="e23c0-264">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="e23c0-265">In diesem Beispiel gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="e23c0-265">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="e23c0-266">Testen von HTTP PATCH-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-266">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-267">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-267">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-268">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-269">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-270">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="e23c0-271">Testen von HTTP HEAD-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-271">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-272">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-272">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-273">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-273">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-274">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-274">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-275">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-275">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="e23c0-276">Testen von HTTP OPTIONS-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-276">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="e23c0-277">Übersicht</span><span class="sxs-lookup"><span data-stu-id="e23c0-277">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="e23c0-278">Argumente</span><span class="sxs-lookup"><span data-stu-id="e23c0-278">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="e23c0-279">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="e23c0-279">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="e23c0-280">Optionen</span><span class="sxs-lookup"><span data-stu-id="e23c0-280">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="e23c0-281">Festlegen von HTTP-Anforderungsheadern</span><span class="sxs-lookup"><span data-stu-id="e23c0-281">Set HTTP request headers</span></span>

<span data-ttu-id="e23c0-282">Verwenden Sie einen der folgenden Ansätze, um einen HTTP-Anforderungsheader festzulegen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-282">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="e23c0-283">Legen Sie den Header inline mit der HTTP-Anforderung fest.</span><span class="sxs-lookup"><span data-stu-id="e23c0-283">Set inline with the HTTP request.</span></span> <span data-ttu-id="e23c0-284">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-284">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="e23c0-285">Beim vorherigen Ansatz benötigt jeder eindeutige HTTP-Anforderungsheader eine eigene `-h`-Option.</span><span class="sxs-lookup"><span data-stu-id="e23c0-285">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="e23c0-286">Legen Sie den Header fest, bevor Sie die HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="e23c0-286">Set before sending the HTTP request.</span></span> <span data-ttu-id="e23c0-287">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-287">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="e23c0-288">Wenn Sie den Header vor dem Senden einer Anforderung festlegen, bleibt er für die Dauer der Befehlsshellsitzung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-288">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="e23c0-289">Geben Sie einen leeren Wert an, um den Header zu löschen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-289">To clear the header, provide an empty value.</span></span> <span data-ttu-id="e23c0-290">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-290">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="e23c0-291">Umschalten der Anzeige von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-291">Toggle HTTP request display</span></span>

<span data-ttu-id="e23c0-292">Standardmäßig wird die Anzeige der gesendeten HTTP-Anforderung unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="e23c0-292">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="e23c0-293">Sie können die entsprechende Einstellung für die Dauer der Befehlsshellsitzung ändern.</span><span class="sxs-lookup"><span data-stu-id="e23c0-293">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="e23c0-294">Aktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-294">Enable request display</span></span>

<span data-ttu-id="e23c0-295">Zeigen Sie die gesendete HTTP-Anforderung an, indem Sie den Befehl `echo on` ausführen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-295">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="e23c0-296">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-296">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="e23c0-297">Nachfolgende HTTP-Anforderungen in der aktuellen Sitzung zeigen die Anforderungsheader an.</span><span class="sxs-lookup"><span data-stu-id="e23c0-297">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="e23c0-298">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-298">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="e23c0-299">Deaktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-299">Disable request display</span></span>

<span data-ttu-id="e23c0-300">Unterdrücken Sie die Anzeige der gesendeten HTTP-Anforderung, indem Sie den Befehl `echo off` ausführen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-300">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="e23c0-301">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-301">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="e23c0-302">Ausführen eines Skripts</span><span class="sxs-lookup"><span data-stu-id="e23c0-302">Run a script</span></span>

<span data-ttu-id="e23c0-303">Wenn Sie häufig die gleichen HTTP REPL-Befehle ausführen, können Sie diese in einer Textdatei speichern.</span><span class="sxs-lookup"><span data-stu-id="e23c0-303">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="e23c0-304">Die Befehle in der Datei haben das gleiche Format wie die manuell in der Befehlszeile ausgeführten Befehle.</span><span class="sxs-lookup"><span data-stu-id="e23c0-304">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="e23c0-305">Die Befehle können mithilfe des Befehls `run` auf einmal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e23c0-305">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="e23c0-306">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-306">For example:</span></span>

1. <span data-ttu-id="e23c0-307">Erstellen Sie eine Textdatei, die einige Befehle enthält, die jeweils in einer neuen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-307">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="e23c0-308">Sie können die Datei *people-script.txt*, die folgende Befehle enthält, als Beispiel verwenden:</span><span class="sxs-lookup"><span data-stu-id="e23c0-308">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="e23c0-309">Führen Sie den Befehl `run` aus, und übergeben Sie den Pfad der Textdatei.</span><span class="sxs-lookup"><span data-stu-id="e23c0-309">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="e23c0-310">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e23c0-310">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="e23c0-311">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e23c0-311">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="e23c0-312">Löschen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="e23c0-312">Clear the output</span></span>

<span data-ttu-id="e23c0-313">Sie können alle Ausgaben des HTTP REPL-Tools aus der Befehlsshell entfernen, indem Sie den Befehl `clear` oder `cls` ausführen.</span><span class="sxs-lookup"><span data-stu-id="e23c0-313">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="e23c0-314">Nehmen Sie beispielsweise an, dass die Befehlsshell folgende Ausgabe enthält:</span><span class="sxs-lookup"><span data-stu-id="e23c0-314">To illustrate, imagine the command shell contains the following output:</span></span>

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="e23c0-315">Führen Sie den folgenden Befehl aus, um die Ausgabe zu löschen:</span><span class="sxs-lookup"><span data-stu-id="e23c0-315">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="e23c0-316">Nachdem der vorherige Befehl ausgeführt wurde, enthält die Befehlsshell nur die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="e23c0-316">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="e23c0-317">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e23c0-317">Additional resources</span></span>

* [<span data-ttu-id="e23c0-318">REST-API-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="e23c0-318">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="e23c0-319">GitHub-Repository für HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="e23c0-319">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
