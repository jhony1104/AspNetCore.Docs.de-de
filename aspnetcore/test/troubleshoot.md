---
title: Problembehandlung für ASP.net Core Projekte
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: b434af2dd046045836d2f6f7f7b7b2d57699bedc
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308279"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="8d110-103">Problembehandlung für ASP.net Core Projekte</span><span class="sxs-lookup"><span data-stu-id="8d110-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="8d110-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d110-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8d110-105">Die folgenden Links enthalten Anleitungen zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="8d110-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="8d110-106">NDC-Konferenz (London, 2018): Diagnostizieren von Problemen in ASP.net Core Anwendungen</span><span class="sxs-lookup"><span data-stu-id="8d110-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="8d110-107">ASP.net-Blog: Problembehandlung bei ASP.net Core Leistungsproblemen</span><span class="sxs-lookup"><span data-stu-id="8d110-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="8d110-108">.Net Core SDK Warnungen</span><span class="sxs-lookup"><span data-stu-id="8d110-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="8d110-109">Die 32-Bit-und 64-Bit-Versionen der .net Core SDK werden installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="8d110-110">Im Dialogfeld " **Neues Projekt** " für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8d110-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="8d110-111">Sowohl 32-Bit-als auch 64-Bit-Versionen der .net Core SDK werden installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="8d110-112">Es werden nur Vorlagen der 64-Bit-Versionen angezeigt, die\\unter "\\C: Programm\\Dateien dotnet\\SDK" installiert sind.</span><span class="sxs-lookup"><span data-stu-id="8d110-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="8d110-113">Diese Warnung wird angezeigt, wenn sowohl die Versionen 32-Bit (x86) als auch 64-Bit (x64) der [.net Core SDK](https://www.microsoft.com/net/download/all) installiert sind.</span><span class="sxs-lookup"><span data-stu-id="8d110-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="8d110-114">Häufige Gründe für die Installation beider Versionen sind u. a.:</span><span class="sxs-lookup"><span data-stu-id="8d110-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="8d110-115">Sie haben ursprünglich den .net Core SDK Installer mithilfe eines 32-Bit-Computers heruntergeladen, ihn jedoch auf einen 64-Bit-Computer kopiert und dort installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="8d110-116">Der 32-Bit-.net Core SDK wurde von einer anderen Anwendung installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="8d110-117">Die falsche Version wurde heruntergeladen und installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="8d110-118">Deinstallieren Sie die 32-Bit-.net Core SDK, um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="8d110-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="8d110-119">Deinstallieren Sie in der **Systemsteuerung** > unter**Programme und Funktionen** > ,**oder ändern Sie ein Programm**.</span><span class="sxs-lookup"><span data-stu-id="8d110-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="8d110-120">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="8d110-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="8d110-121">Die .net Core SDK wird an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="8d110-122">Im Dialogfeld " **Neues Projekt** " für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8d110-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="8d110-123">Der .net Core SDK wird an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="8d110-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="8d110-124">Es werden nur Vorlagen aus den SDKs angezeigt, die\\unter "\\C: Programm\\Dateien dotnet\\SDK" installiert sind.</span><span class="sxs-lookup"><span data-stu-id="8d110-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="8d110-125">Diese Meldung wird angezeigt, wenn mindestens eine Installation des .net Core SDK in einem Verzeichnis außerhalb von *C:\\Program Files\\\\dotnet SDK\\* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8d110-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="8d110-126">Dies geschieht normalerweise, wenn die .net Core SDK auf einem Computer mithilfe von Kopieren/Einfügen anstelle des MSI-Installers bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="8d110-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="8d110-127">Deinstallieren Sie alle 32-Bit .net Core sdchs und Laufzeiten, um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="8d110-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="8d110-128">Deinstallieren Sie in der **Systemsteuerung** > unter**Programme und Funktionen** > ,**oder ändern Sie ein Programm**.</span><span class="sxs-lookup"><span data-stu-id="8d110-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="8d110-129">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="8d110-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="8d110-130">Es wurden keine .net Core-sdche erkannt.</span><span class="sxs-lookup"><span data-stu-id="8d110-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="8d110-131">Im Dialogfeld " **Neues Projekt** " in Visual Studio für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8d110-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="8d110-132">Es wurden keine .net Core sdys erkannt. Stellen Sie sicher, dass Sie in `PATH`der Umgebungsvariablen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="8d110-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="8d110-133">Wenn Sie einen `dotnet` Befehl ausführen, wird die Warnung wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8d110-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="8d110-134">Es war nicht möglich, installierte dotnet-sdche zu finden.</span><span class="sxs-lookup"><span data-stu-id="8d110-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="8d110-135">Diese Warnungen werden angezeigt, wenn die `PATH` Umgebungsvariable nicht auf .net Core-sdche auf dem Computer verweist.</span><span class="sxs-lookup"><span data-stu-id="8d110-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="8d110-136">So beheben Sie dieses Problem:</span><span class="sxs-lookup"><span data-stu-id="8d110-136">To resolve this problem:</span></span>

* <span data-ttu-id="8d110-137">Installieren Sie das .NET Core-SDK.</span><span class="sxs-lookup"><span data-stu-id="8d110-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="8d110-138">Holen Sie sich den neuesten Installer über [.net-Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="8d110-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="8d110-139">Vergewissern Sie sich `PATH` , dass die Umgebungsvariable auf den Speicherort verweist, an`C:\Program Files\dotnet\` dem das SDK installiert ist (für `C:\Program Files (x86)\dotnet\` 64-Bit/x64 oder 32-Bit/x86).</span><span class="sxs-lookup"><span data-stu-id="8d110-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="8d110-140">Der SDK-Installer legt Normal `PATH`erweise den fest.</span><span class="sxs-lookup"><span data-stu-id="8d110-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="8d110-141">Installieren Sie immer die gleichen bitangaben und-Laufzeiten auf dem gleichen Computer.</span><span class="sxs-lookup"><span data-stu-id="8d110-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="8d110-142">Fehlendes SDK nach Installation des .net Core-hostingpakets</span><span class="sxs-lookup"><span data-stu-id="8d110-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="8d110-143">Durch die Installation des [.net Core-hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) wird der `PATH` geändert, wenn die .net Core-Laufzeit installiert wird, um auf die 32-Bit-Version`C:\Program Files (x86)\dotnet\`(x86) von .net Core () zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="8d110-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="8d110-144">Dies kann zu fehlenden sdten führen, wenn der .net Core `dotnet` -Befehl 32-Bit (x86) verwendet wird (es[wurden keine .net Core sdert erkannt](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="8d110-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="8d110-145">Um dieses Problem zu beheben, `C:\Program Files\dotnet\` wechseln Sie zu einer `C:\Program Files (x86)\dotnet\` Position vor `PATH`auf dem.</span><span class="sxs-lookup"><span data-stu-id="8d110-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="8d110-146">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="8d110-146">Obtain data from an app</span></span>

<span data-ttu-id="8d110-147">Wenn eine APP auf Anforderungen reagieren kann, können Sie die folgenden Daten über die Middleware von der App abrufen:</span><span class="sxs-lookup"><span data-stu-id="8d110-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="8d110-148">Anforderungs &ndash; Methode, Schema, Host, pathbase, Pfad, Abfrage Zeichenfolge, Header</span><span class="sxs-lookup"><span data-stu-id="8d110-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="8d110-149">Remote &ndash; -IP-Adresse der Verbindung, Remoteport, lokale IP-Adresse, lokaler Port, Client Zertifikat</span><span class="sxs-lookup"><span data-stu-id="8d110-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="8d110-150">Identitäts &ndash; Name, Anzeige Name</span><span class="sxs-lookup"><span data-stu-id="8d110-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="8d110-151">Konfigurationseinstellungen</span><span class="sxs-lookup"><span data-stu-id="8d110-151">Configuration settings</span></span>
* <span data-ttu-id="8d110-152">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8d110-152">Environment variables</span></span>

<span data-ttu-id="8d110-153">Platzieren Sie den [](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) folgenden middlewarescode am Anfang der `Startup.Configure` Anforderungs Verarbeitungs Pipeline der Methode.</span><span class="sxs-lookup"><span data-stu-id="8d110-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="8d110-154">Die Umgebung wird vor der Ausführung der Middleware geprüft, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8d110-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="8d110-155">Verwenden Sie zum Abrufen der Umgebung einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="8d110-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="8d110-156">Fügen Sie `Startup.Configure` in die-Methode ein, und überprüfen Sie die Umgebung mit der lokalen Variablen. `IHostingEnvironment`</span><span class="sxs-lookup"><span data-stu-id="8d110-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="8d110-157">Der folgende Beispielcode veranschaulicht diese Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="8d110-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="8d110-158">Weisen Sie die Umgebung einer Eigenschaft in der `Startup` -Klasse zu.</span><span class="sxs-lookup"><span data-stu-id="8d110-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="8d110-159">Überprüfen Sie die Umgebung mithilfe der-Eigenschaft ( `if (Environment.IsDevelopment())`z. b.).</span><span class="sxs-lookup"><span data-stu-id="8d110-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```
