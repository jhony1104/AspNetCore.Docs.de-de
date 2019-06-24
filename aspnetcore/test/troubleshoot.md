---
title: Problembehandlung bei ASP.NET Core-Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: test/troubleshoot
ms.openlocfilehash: bcec8a55a5111e1f3acf53ae2f57b45e6e609d25
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313671"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="d8224-103">Problembehandlung bei ASP.NET Core-Projekten</span><span class="sxs-lookup"><span data-stu-id="d8224-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="d8224-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8224-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8224-105">Die folgenden Links erhalten Anleitungen zur Fehlerbehebung:</span><span class="sxs-lookup"><span data-stu-id="d8224-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="d8224-106">NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="d8224-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="d8224-107">ASP.NET-Blog: Behandlung von Leistungsproblemen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8224-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="d8224-108">.NET Core SDK-Warnungen</span><span class="sxs-lookup"><span data-stu-id="d8224-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="d8224-109">Es werden sowohl die 32-Bit und 64-Bit-Versionen von .NET Core SDK installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="d8224-110">In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d8224-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d8224-111">Es werden sowohl 32-Bit- und 64-Bit-Versionen von .NET Core SDK installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="d8224-112">Nur Vorlagen von den 64-Bit-Versionen installiert ' "c:"\\Programmdateien\\Dotnet\\Sdk\\"werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d8224-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d8224-113">Diese Warnung wird angezeigt, wenn sowohl die 32-Bit-(x86) als auch die 64-Bit (x 64) Versionen der [.NET Core SDK](https://www.microsoft.com/net/download/all) installiert sind.</span><span class="sxs-lookup"><span data-stu-id="d8224-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="d8224-114">Häufige Gründe, die beide Versionen installiert sind enthalten:</span><span class="sxs-lookup"><span data-stu-id="d8224-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="d8224-115">Sie ursprünglich heruntergeladen das .NET Core SDK-Installationsprogramm, das mit einem 32-Bit-Computer, aber klicken Sie dann über kopiert und es auf einem 64-Bit-Computer installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="d8224-116">Das 32-Bit .NET Core SDK wurde durch eine andere Anwendung installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="d8224-117">Die falsche Version wurde heruntergeladen und installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="d8224-118">Deinstallieren Sie die 32-Bit .NET Core-SDK, um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d8224-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="d8224-119">Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**.</span><span class="sxs-lookup"><span data-stu-id="d8224-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d8224-120">Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="d8224-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="d8224-121">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="d8224-122">In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d8224-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="d8224-123">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="d8224-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="d8224-124">Nur Vorlagen aus den SDKs installiert ' "c:"\\Programmdateien\\Dotnet\\Sdk\\"werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d8224-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="d8224-125">Diese Meldung angezeigt, wenn Sie mindestens eine Installation von .NET Core SDK in einem Verzeichnis außerhalb des haben *C:\\Programmdateien\\Dotnet\\Sdk\\* .</span><span class="sxs-lookup"><span data-stu-id="d8224-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="d8224-126">Dies geschieht normalerweise, wenn das .NET Core SDK auf einem Computer mit Kopieren/Einfügen, anstatt das MSI-Installationsprogramm bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d8224-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="d8224-127">Deinstallieren Sie alle 32-Bit .NET Core SDKs und Laufzeiten um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="d8224-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="d8224-128">Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**.</span><span class="sxs-lookup"><span data-stu-id="d8224-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="d8224-129">Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="d8224-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="d8224-130">Es wurden keine .NET Core SDKs erkannt.</span><span class="sxs-lookup"><span data-stu-id="d8224-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="d8224-131">In der Visual Studio **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d8224-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="d8224-132">Es wurden keine .NET Core SDKs erkannt, stellen Sie sicher, sie befinden sich in der Umgebungsvariablen `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d8224-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="d8224-133">Beim Ausführen einer `dotnet` können die Warnung wird angezeigt als:</span><span class="sxs-lookup"><span data-stu-id="d8224-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="d8224-134">Es war nicht möglich, alle installierten Dotnet SDKs zu finden.</span><span class="sxs-lookup"><span data-stu-id="d8224-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="d8224-135">Diese Warnungen angezeigt, wenn die Umgebungsvariable `PATH` verweist nicht auf alle .NET Core-SDKs auf dem Computer.</span><span class="sxs-lookup"><span data-stu-id="d8224-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="d8224-136">Um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="d8224-136">To resolve this problem:</span></span>

* <span data-ttu-id="d8224-137">Installieren Sie das .NET Core-SDK.</span><span class="sxs-lookup"><span data-stu-id="d8224-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="d8224-138">Abrufen der neuesten Installation von [.NET Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="d8224-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="d8224-139">Überprüfen Sie, ob die `PATH` Umgebungsvariable verweist auf den Speicherort, in dem das SDK installiert ist (`C:\Program Files\dotnet\` für 64-Bit/X64 oder `C:\Program Files (x86)\dotnet\` für 32-Bit/X86).</span><span class="sxs-lookup"><span data-stu-id="d8224-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="d8224-140">Das SDK-Installationsprogramm normalerweise legt der `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d8224-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="d8224-141">Installieren Sie immer die gleiche Bitness SDKs und Laufzeiten auf dem gleichen Computer.</span><span class="sxs-lookup"><span data-stu-id="d8224-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="d8224-142">Fehlendes SDK nach der Installation von .NET Core Hosting-Paket</span><span class="sxs-lookup"><span data-stu-id="d8224-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="d8224-143">Installieren der [Paket für .NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) ändert die `PATH` wann es installiert die .NET Core-Runtime, um auf die 32-Bit (x 86)-Version von .NET Core zu verweisen (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="d8224-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="d8224-144">Dies kann dazu führen fehlende SDKs bei 32-Bit (x 86) .NET Core `dotnet` -Befehl wird verwendet, ([wurden keine .NET Core SDKs erkannt](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="d8224-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="d8224-145">Um dieses Problem zu beheben, verschieben `C:\Program Files\dotnet\` an eine Position vor dem `C:\Program Files (x86)\dotnet\` auf die `PATH`.</span><span class="sxs-lookup"><span data-stu-id="d8224-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="d8224-146">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="d8224-146">Obtain data from an app</span></span>

<span data-ttu-id="d8224-147">Wenn eine app auf Anforderungen reagiert werden kann, können Sie die folgenden Daten aus der app unter Verwendung von Middleware abrufen:</span><span class="sxs-lookup"><span data-stu-id="d8224-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="d8224-148">Anforderung &ndash; Methode, Schema, Host, Pathbase, Pfad, Abfragezeichenfolge, Header</span><span class="sxs-lookup"><span data-stu-id="d8224-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="d8224-149">Verbindung &ndash; Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Client-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="d8224-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="d8224-150">Identität &ndash; Namen, Anzeigenamen</span><span class="sxs-lookup"><span data-stu-id="d8224-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="d8224-151">Konfigurationseinstellungen</span><span class="sxs-lookup"><span data-stu-id="d8224-151">Configuration settings</span></span>
* <span data-ttu-id="d8224-152">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="d8224-152">Environment variables</span></span>

<span data-ttu-id="d8224-153">Fügen Sie Folgendes [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) Code am Anfang der `Startup.Configure` Pipeline zur anforderungsverarbeitung-Methode.</span><span class="sxs-lookup"><span data-stu-id="d8224-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="d8224-154">Die Umgebung wird überprüft, bevor die Middleware ausgeführt wird, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d8224-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="d8224-155">Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="d8224-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="d8224-156">Einfügen der `IHostingEnvironment` in die `Startup.Configure` -Methode, und überprüfen Sie die Umgebung mit der lokalen Variablen.</span><span class="sxs-lookup"><span data-stu-id="d8224-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="d8224-157">Der folgende Code veranschaulicht diesen Ansatz.</span><span class="sxs-lookup"><span data-stu-id="d8224-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="d8224-158">Weisen Sie die Umgebung zu einer Eigenschaft in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d8224-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="d8224-159">Überprüfen Sie die Umgebung, die mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="d8224-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
