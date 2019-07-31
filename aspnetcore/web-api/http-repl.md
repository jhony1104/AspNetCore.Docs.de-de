---
title: Testen von Web-APIs mit HTTP REPL
author: scottaddie
description: Hier erfahren Sie, wie Sie das globale .NET Core-Tool HTTP REPL verwenden können, um ASP.NET Core-Web-APIs zu durchsuchen und zu testen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2019
uid: web-api/http-repl
ms.openlocfilehash: 1ceda6182c62bb1be06cd95f14e6a46a1809253e
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410887"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="949df-103">Testen von Web-APIs mit HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="949df-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="949df-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="949df-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="949df-105">HTTP REPL (read–eval–print-Loop):</span><span class="sxs-lookup"><span data-stu-id="949df-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="949df-106">Ein schlankes, plattformübergreifendes Befehlszeilentool, das überall unterstützt wird, wo .NET Core unterstützt wird</span><span class="sxs-lookup"><span data-stu-id="949df-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="949df-107">Wird zum Senden von HTTP-Anforderungen zum Testen von ASP.NET Core-Web-APIs und zum Überprüfen von deren Ergebnissen verwendet</span><span class="sxs-lookup"><span data-stu-id="949df-107">Used for making HTTP requests to test ASP.NET Core web APIs and view their results.</span></span>

<span data-ttu-id="949df-108">Folgende [HTTP-Verben](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="949df-108">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="949df-109">DELETE</span><span class="sxs-lookup"><span data-stu-id="949df-109">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="949df-110">GET</span><span class="sxs-lookup"><span data-stu-id="949df-110">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="949df-111">HEAD</span><span class="sxs-lookup"><span data-stu-id="949df-111">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="949df-112">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="949df-112">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="949df-113">PATCH</span><span class="sxs-lookup"><span data-stu-id="949df-113">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="949df-114">POST</span><span class="sxs-lookup"><span data-stu-id="949df-114">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="949df-115">PUT</span><span class="sxs-lookup"><span data-stu-id="949df-115">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="949df-116">[Sehen Sie sich die beispielhafte ASP.NET Core-Web-API an, oder laden Sie sie herunter ([Downloadanleitung](xref:index#how-to-download-a-sample))](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples), um dem Artikel zu folgen.</span><span class="sxs-lookup"><span data-stu-id="949df-116">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="949df-117">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="949df-117">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="949df-118">Installation</span><span class="sxs-lookup"><span data-stu-id="949df-118">Installation</span></span>

<span data-ttu-id="949df-119">Führen Sie den folgenden Befehl aus, um HTTP REPL zu installieren:</span><span class="sxs-lookup"><span data-stu-id="949df-119">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version 3.0.0-*
```

<span data-ttu-id="949df-120">Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) installiert.</span><span class="sxs-lookup"><span data-stu-id="949df-120">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="949df-121">Verwendung</span><span class="sxs-lookup"><span data-stu-id="949df-121">Usage</span></span>

<span data-ttu-id="949df-122">Nach der erfolgreichen Installation des Tools können Sie folgenden Befehl ausführen, um HTTP REPL zu starten:</span><span class="sxs-lookup"><span data-stu-id="949df-122">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="949df-123">Führen Sie einen der folgenden Befehle aus, um eine Liste der verfügbaren HTTP REPL-Befehle anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="949df-123">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="949df-124">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-124">The following output is displayed:</span></span>

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

<span data-ttu-id="949df-125">In HTTP REPL können Befehle vervollständigt werden.</span><span class="sxs-lookup"><span data-stu-id="949df-125">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="949df-126">Wenn Sie die <kbd>TAB-TASTE</kbd> drücken, können Sie die Liste der Befehle durchlaufen, die die eingegebenen Zeichen oder API-Endpunkte vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="949df-126">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="949df-127">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="949df-127">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="949df-128">Herstellen einer Verbindung mit der Web-API</span><span class="sxs-lookup"><span data-stu-id="949df-128">Connect to the web API</span></span>

<span data-ttu-id="949df-129">Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit einer Web-API her:</span><span class="sxs-lookup"><span data-stu-id="949df-129">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="949df-130">`<BASE URI>` ist der Basis-URI für die Web-API.</span><span class="sxs-lookup"><span data-stu-id="949df-130">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="949df-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-131">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="949df-132">Alternativ können Sie den folgenden Befehl jederzeit ausführen, während HTTP REPL ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="949df-132">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="949df-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-133">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="949df-134">Verweis auf ein Swagger-Dokument für die Web-API</span><span class="sxs-lookup"><span data-stu-id="949df-134">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="949df-135">Legen Sie den relativen URI auf das Swagger-Dokument für die Web-API fest, um diese ordnungsgemäß zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="949df-135">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="949df-136">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="949df-136">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="949df-137">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-137">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="949df-138">Navigieren in der Web-API</span><span class="sxs-lookup"><span data-stu-id="949df-138">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="949df-139">Anzeigen verfügbarer Endpunkte</span><span class="sxs-lookup"><span data-stu-id="949df-139">View available endpoints</span></span>

<span data-ttu-id="949df-140">Führen Sie den Befehl `ls` oder `dir` aus, um die unterschiedlichen Endpunkte (Controller) im aktuellen Pfad der Web-API-Adresse aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="949df-140">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="949df-141">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-141">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="949df-142">Die vorherige Ausgabe gibt an, dass die zwei Controller `Fruits` und `People` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="949df-142">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="949df-143">Beide Controller unterstützen parameterlose HTTP GET- und HTTP POST-Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="949df-143">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="949df-144">Wenn Sie zu einem bestimmten Controller navigieren, werden weitere Details angezeigt.</span><span class="sxs-lookup"><span data-stu-id="949df-144">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="949df-145">Aus der Ausgabe des folgenden Befehls geht beispielsweise hervor, dass der Controller `Fruits` auch HTTP GET-, HTTP PUT- und HTTP DELETE-Vorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="949df-145">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="949df-146">Jeder dieser Vorgänge erwartet einen `id`-Parameter in der Route:</span><span class="sxs-lookup"><span data-stu-id="949df-146">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="949df-147">Führen Sie alternativ den Befehl `ui` aus, um die Swagger-Benutzeroberflächenseite der Web-API in einem Browser zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="949df-147">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="949df-148">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-148">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="949df-149">Navigieren zu einem Endpunkt</span><span class="sxs-lookup"><span data-stu-id="949df-149">Navigate to an endpoint</span></span>

<span data-ttu-id="949df-150">Führen Sie den Befehl `cd` aus, um zu einem anderen Endpunkt in der Web-API zu navigieren:</span><span class="sxs-lookup"><span data-stu-id="949df-150">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="949df-151">Für den Pfad im Befehl `cd` wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="949df-151">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="949df-152">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-152">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="949df-153">Anpassen von HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="949df-153">Customize the HTTP REPL</span></span>

<span data-ttu-id="949df-154">Die [Standardfarben](#set-color-preferences) von HTTP REPL können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="949df-154">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="949df-155">Außerdem kann definiert werden, welcher [Text-Editor](#set-the-default-text-editor) standardmäßig verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-155">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="949df-156">Die HTTP REPL-Einstellungen werden in der aktuellen Sitzung beibehalten und in zukünftigen Sitzungen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="949df-156">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="949df-157">Nach der Änderung werden die Einstellungen in der folgenden Datei gespeichert:</span><span class="sxs-lookup"><span data-stu-id="949df-157">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="949df-158">Linux</span><span class="sxs-lookup"><span data-stu-id="949df-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="949df-159">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="949df-159">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="949df-160">macOS</span><span class="sxs-lookup"><span data-stu-id="949df-160">macOS</span></span>](#tab/macos)

<span data-ttu-id="949df-161">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="949df-161">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="949df-162">Windows</span><span class="sxs-lookup"><span data-stu-id="949df-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="949df-163">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="949df-163">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="949df-164">Die *HTTPREPLPREFS-Datei* wird beim Start geladen und bei der Laufzeit nicht auf Änderungen überwacht.</span><span class="sxs-lookup"><span data-stu-id="949df-164">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="949df-165">Manuelle Änderungen an der Datei werden erst nach dem Neustart des Tools wirksam.</span><span class="sxs-lookup"><span data-stu-id="949df-165">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="949df-166">Anzeigen der Einstellungen</span><span class="sxs-lookup"><span data-stu-id="949df-166">View the settings</span></span>

<span data-ttu-id="949df-167">Führen Sie den Befehl `pref get` aus, um die verfügbaren Einstellungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="949df-167">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="949df-168">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-168">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="949df-169">Mit dem vorangehenden Befehl werden die verfügbaren Schlüssel-Wert-Paare angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-169">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="949df-170">Festlegen von Farbeinstellungen</span><span class="sxs-lookup"><span data-stu-id="949df-170">Set color preferences</span></span>

<span data-ttu-id="949df-171">Die farbliche Kennzeichnung von Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="949df-171">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="949df-172">Sie können die Standardfarben des HTTP REPL-Tools anpassen, indem Sie den Schlüssel für die entsprechende Farbe suchen und ändern.</span><span class="sxs-lookup"><span data-stu-id="949df-172">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="949df-173">Weitere Informationen zum Suchen von Schlüsseln finden Sie im Abschnitt [Anzeigen der Einstellungen](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="949df-173">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="949df-174">Ändern Sie beispielsweise den Wert des Schlüssels `colors.json` folgendermaßen von `Green` in `White`:</span><span class="sxs-lookup"><span data-stu-id="949df-174">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="949df-175">Es können nur die [zulässigen Farben](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="949df-175">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="949df-176">Nachfolgende HTTP-Anforderungen zeigen die Ausgabe mit der neuen Farbgebung an.</span><span class="sxs-lookup"><span data-stu-id="949df-176">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="949df-177">Wenn bestimmte Farbtasten nicht festgelegt sind, werden mehr generische Schlüssel berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="949df-177">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="949df-178">Anhand des folgenden Beispiels wird das Fallbackverhalten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="949df-178">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="949df-179">Wenn `colors.json.name` keinen Wert aufweist, wird `colors.json.string` verwendet.</span><span class="sxs-lookup"><span data-stu-id="949df-179">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="949df-180">Wenn `colors.json.string` keinen Wert aufweist, wird `colors.json.literal` verwendet.</span><span class="sxs-lookup"><span data-stu-id="949df-180">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="949df-181">Wenn `colors.json.literal` keinen Wert aufweist, wird `colors.json` verwendet.</span><span class="sxs-lookup"><span data-stu-id="949df-181">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="949df-182">Wenn `colors.json` keinen Wert aufweist, wird die Standardtextfarbe (`AllowedColors.None`) der Befehlsshell verwendet.</span><span class="sxs-lookup"><span data-stu-id="949df-182">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="949df-183">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="949df-183">Set indentation size</span></span>

<span data-ttu-id="949df-184">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="949df-184">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="949df-185">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="949df-185">The default size is two spaces.</span></span> <span data-ttu-id="949df-186">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-186">For example:</span></span>

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

<span data-ttu-id="949df-187">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="949df-187">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="949df-188">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="949df-188">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="949df-189">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="949df-189">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="949df-190">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="949df-190">Set indentation size</span></span>

<span data-ttu-id="949df-191">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="949df-191">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="949df-192">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="949df-192">The default size is two spaces.</span></span> <span data-ttu-id="949df-193">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-193">For example:</span></span>

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

<span data-ttu-id="949df-194">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="949df-194">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="949df-195">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="949df-195">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="949df-196">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="949df-196">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="949df-197">Festlegen des Standard-Text-Editors</span><span class="sxs-lookup"><span data-stu-id="949df-197">Set the default text editor</span></span>

<span data-ttu-id="949df-198">Standardmäßig ist für HTTP REPL kein Text-Editor konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="949df-198">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="949df-199">Wenn Sie Web-API-Methoden testen möchten, für die ein HTTP-Anforderungstext erforderlich ist, müssen Sie einen Standard-Text-Editor festlegen.</span><span class="sxs-lookup"><span data-stu-id="949df-199">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="949df-200">Das HTTP REPL-Tool startet den konfigurierten Text-Editor nur zum Verfassen des Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="949df-200">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="949df-201">Führen Sie den folgenden Befehl aus, um Ihren bevorzugten Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="949df-201">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="949df-202">Im vorherigen Befehl ist `<EXECUTABLE>` der vollständige Pfad zur ausführbaren Datei des Text-Editors.</span><span class="sxs-lookup"><span data-stu-id="949df-202">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="949df-203">Führen Sie beispielsweise folgenden Befehl aus, um Visual Studio Code als Standard-Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="949df-203">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="949df-204">Linux</span><span class="sxs-lookup"><span data-stu-id="949df-204">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="949df-205">macOS</span><span class="sxs-lookup"><span data-stu-id="949df-205">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="949df-206">Windows</span><span class="sxs-lookup"><span data-stu-id="949df-206">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="949df-207">Legen Sie den Schlüssel `editor.command.default.arguments` fest, um den Standard-Text-Editor mit bestimmten CLI-Argumenten zu starten.</span><span class="sxs-lookup"><span data-stu-id="949df-207">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="949df-208">Nehmen Sie beispielsweise an, dass Visual Studio Code der Standard-Text-Editor ist und Sie möchten, dass Visual Studio Code durch HTTP REPL immer in einer neuen Sitzung mit deaktivierten Erweiterungen öffnet.</span><span class="sxs-lookup"><span data-stu-id="949df-208">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="949df-209">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="949df-209">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="949df-210">Testen von HTTP GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-210">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-211">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-211">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-212">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-212">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-213">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-213">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-214">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-214">Options</span></span>

<span data-ttu-id="949df-215">Für den Befehl `get` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="949df-215">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="949df-216">Beispiel</span><span class="sxs-lookup"><span data-stu-id="949df-216">Example</span></span>

<span data-ttu-id="949df-217">So führen Sie eine HTTP GET-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="949df-217">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="949df-218">Führen Sie den Befehl `get` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="949df-218">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="949df-219">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-219">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="949df-220">Rufen Sie einen bestimmten Datensatz ab, indem Sie einen Parameter an den Befehl `get` übergeben:</span><span class="sxs-lookup"><span data-stu-id="949df-220">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="949df-221">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-221">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="949df-222">Testen von HTTP POST-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-222">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-223">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-223">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-224">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-225">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-226">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-226">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="949df-227">Beispiel</span><span class="sxs-lookup"><span data-stu-id="949df-227">Example</span></span>

<span data-ttu-id="949df-228">So führen Sie eine HTTP POST-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="949df-228">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="949df-229">Führen Sie den Befehl `post` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="949df-229">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="949df-230">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="949df-230">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="949df-231">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="949df-231">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="949df-232">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-232">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="949df-233">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="949df-233">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="949df-234">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="949df-234">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="949df-235">Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="949df-235">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="949df-236">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-236">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="949df-237">Testen von HTTP PUT-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-237">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-238">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-238">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-239">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-239">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-240">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-240">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-241">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-241">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="949df-242">Beispiel</span><span class="sxs-lookup"><span data-stu-id="949df-242">Example</span></span>

<span data-ttu-id="949df-243">So führen Sie eine HTTP PUT-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="949df-243">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="949df-244">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="949df-244">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="949df-245">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="949df-245">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="949df-246">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="949df-246">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="949df-247">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-247">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="949df-248">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="949df-248">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="949df-249">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="949df-249">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="949df-250">Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="949df-250">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="949df-251">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-251">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="949df-252">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="949df-252">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="949df-253">Wenn Sie beispielsweise „Cherry“ in den Text-Editor eingeben, gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="949df-253">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="949df-254">Testen von HTTP DELETE-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-254">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-255">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-255">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-256">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-256">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-257">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-257">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-258">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-258">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="949df-259">Beispiel</span><span class="sxs-lookup"><span data-stu-id="949df-259">Example</span></span>

<span data-ttu-id="949df-260">So führen Sie eine HTTP DELETE-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="949df-260">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="949df-261">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="949df-261">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="949df-262">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-262">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="949df-263">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="949df-263">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="949df-264">In diesem Beispiel gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="949df-264">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="949df-265">Testen von HTTP PATCH-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-265">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-266">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-266">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-267">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-267">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-268">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-268">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-269">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-269">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="949df-270">Testen von HTTP HEAD-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-270">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-271">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-271">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-272">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-272">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-273">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-273">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-274">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-274">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="949df-275">Testen von HTTP OPTIONS-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-275">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="949df-276">Übersicht</span><span class="sxs-lookup"><span data-stu-id="949df-276">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="949df-277">Argumente</span><span class="sxs-lookup"><span data-stu-id="949df-277">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="949df-278">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="949df-278">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="949df-279">Optionen</span><span class="sxs-lookup"><span data-stu-id="949df-279">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="949df-280">Festlegen von HTTP-Anforderungsheadern</span><span class="sxs-lookup"><span data-stu-id="949df-280">Set HTTP request headers</span></span>

<span data-ttu-id="949df-281">Verwenden Sie einen der folgenden Ansätze, um einen HTTP-Anforderungsheader festzulegen:</span><span class="sxs-lookup"><span data-stu-id="949df-281">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="949df-282">Legen Sie den Header inline mit der HTTP-Anforderung fest.</span><span class="sxs-lookup"><span data-stu-id="949df-282">Set inline with the HTTP request.</span></span> <span data-ttu-id="949df-283">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-283">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="949df-284">Beim vorherigen Ansatz benötigt jeder eindeutige HTTP-Anforderungsheader eine eigene `-h`-Option.</span><span class="sxs-lookup"><span data-stu-id="949df-284">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="949df-285">Legen Sie den Header fest, bevor Sie die HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="949df-285">Set before sending the HTTP request.</span></span> <span data-ttu-id="949df-286">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-286">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="949df-287">Wenn Sie den Header vor dem Senden einer Anforderung festlegen, bleibt er für die Dauer der Befehlsshellsitzung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="949df-287">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="949df-288">Geben Sie einen leeren Wert an, um den Header zu löschen.</span><span class="sxs-lookup"><span data-stu-id="949df-288">To clear the header, provide an empty value.</span></span> <span data-ttu-id="949df-289">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-289">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="949df-290">Umschalten der Anzeige von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-290">Toggle HTTP request display</span></span>

<span data-ttu-id="949df-291">Standardmäßig wird die Anzeige der gesendeten HTTP-Anforderung unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="949df-291">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="949df-292">Sie können die entsprechende Einstellung für die Dauer der Befehlsshellsitzung ändern.</span><span class="sxs-lookup"><span data-stu-id="949df-292">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="949df-293">Aktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-293">Enable request display</span></span>

<span data-ttu-id="949df-294">Zeigen Sie die gesendete HTTP-Anforderung an, indem Sie den Befehl `echo on` ausführen.</span><span class="sxs-lookup"><span data-stu-id="949df-294">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="949df-295">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-295">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="949df-296">Nachfolgende HTTP-Anforderungen in der aktuellen Sitzung zeigen die Anforderungsheader an.</span><span class="sxs-lookup"><span data-stu-id="949df-296">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="949df-297">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-297">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="949df-298">Deaktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-298">Disable request display</span></span>

<span data-ttu-id="949df-299">Unterdrücken Sie die Anzeige der gesendeten HTTP-Anforderung, indem Sie den Befehl `echo off` ausführen.</span><span class="sxs-lookup"><span data-stu-id="949df-299">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="949df-300">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-300">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="949df-301">Ausführen eines Skripts</span><span class="sxs-lookup"><span data-stu-id="949df-301">Run a script</span></span>

<span data-ttu-id="949df-302">Wenn Sie häufig die gleichen HTTP REPL-Befehle ausführen, können Sie diese in einer Textdatei speichern.</span><span class="sxs-lookup"><span data-stu-id="949df-302">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="949df-303">Die Befehle in der Datei haben das gleiche Format wie die manuell in der Befehlszeile ausgeführten Befehle.</span><span class="sxs-lookup"><span data-stu-id="949df-303">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="949df-304">Die Befehle können mithilfe des Befehls `run` auf einmal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="949df-304">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="949df-305">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-305">For example:</span></span>

1. <span data-ttu-id="949df-306">Erstellen Sie eine Textdatei, die einige Befehle enthält, die jeweils in einer neuen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="949df-306">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="949df-307">Sie können die Datei *people-script.txt*, die folgende Befehle enthält, als Beispiel verwenden:</span><span class="sxs-lookup"><span data-stu-id="949df-307">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="949df-308">Führen Sie den Befehl `run` aus, und übergeben Sie den Pfad der Textdatei.</span><span class="sxs-lookup"><span data-stu-id="949df-308">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="949df-309">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="949df-309">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="949df-310">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="949df-310">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="949df-311">Löschen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="949df-311">Clear the output</span></span>

<span data-ttu-id="949df-312">Sie können alle Ausgaben des HTTP REPL-Tools aus der Befehlsshell entfernen, indem Sie den Befehl `clear` oder `cls` ausführen.</span><span class="sxs-lookup"><span data-stu-id="949df-312">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="949df-313">Nehmen Sie beispielsweise an, dass die Befehlsshell folgende Ausgabe enthält:</span><span class="sxs-lookup"><span data-stu-id="949df-313">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="949df-314">Führen Sie den folgenden Befehl aus, um die Ausgabe zu löschen:</span><span class="sxs-lookup"><span data-stu-id="949df-314">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="949df-315">Nachdem der vorherige Befehl ausgeführt wurde, enthält die Befehlsshell nur die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="949df-315">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="949df-316">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="949df-316">Additional resources</span></span>

* [<span data-ttu-id="949df-317">REST-API-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="949df-317">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="949df-318">GitHub-Repository für HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="949df-318">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
