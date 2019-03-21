---
title: Problembehandlung bei ASP.NET Core-Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: 1f53375a6aa504c989920a0bff83b400b7bd83ec
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208842"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="4e739-103">Problembehandlung bei ASP.NET Core-Projekten</span><span class="sxs-lookup"><span data-stu-id="4e739-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="4e739-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e739-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4e739-105">Die folgenden Links erhalten Anleitungen zur Fehlerbehebung:</span><span class="sxs-lookup"><span data-stu-id="4e739-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="4e739-106">NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="4e739-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="4e739-107">ASP.NET-Blog: Behandlung von Leistungsproblemen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e739-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="4e739-108">.NET Core SDK-Warnungen</span><span class="sxs-lookup"><span data-stu-id="4e739-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="4e739-109">Die 32-Bit und 64-Bit-Versionen von .NET Core SDK sind installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="4e739-110">In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4e739-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4e739-111">Es werden sowohl 32- und 64-Bit-Versionen von .NET Core SDK installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="4e739-112">Nur Vorlagen aus die 64-Bit-Versionen installiert ' "c:"\\Programmdateien\\Dotnet\\Sdk\\"wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4e739-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/both32and64bit.png)

<span data-ttu-id="4e739-114">Diese Warnung wird angezeigt, wenn sowohl die 32-Bit-(x86) als auch die 64-Bit (x 64) Versionen der [.NET Core SDK](https://www.microsoft.com/net/download/all) installiert sind.</span><span class="sxs-lookup"><span data-stu-id="4e739-114">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="4e739-115">Häufige Gründe, die beide Versionen installiert sind enthalten:</span><span class="sxs-lookup"><span data-stu-id="4e739-115">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="4e739-116">Sie ursprünglich heruntergeladen das .NET Core SDK-Installationsprogramm, das mit einem 32-Bit-Computer, aber klicken Sie dann über kopiert und es auf einem 64-Bit-Computer installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-116">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="4e739-117">Das 32-Bit .NET Core SDK wurde durch eine andere Anwendung installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-117">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="4e739-118">Die falsche Version wurde heruntergeladen und installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-118">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="4e739-119">Deinstallieren Sie die 32-Bit .NET Core-SDK, um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4e739-119">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="4e739-120">Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**.</span><span class="sxs-lookup"><span data-stu-id="4e739-120">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4e739-121">Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="4e739-121">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="4e739-122">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-122">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="4e739-123">In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4e739-123">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4e739-124">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="4e739-124">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="4e739-125">Nur Vorlagen aus dem SDK unter "C:\\Programmdateien\\Dotnet\\Sdk\\" wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4e739-125">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/multiplelocations.png)

<span data-ttu-id="4e739-127">Diese Meldung angezeigt, wenn Sie mindestens eine Installation von .NET Core SDK in einem Verzeichnis außerhalb des haben *C:\\Programmdateien\\Dotnet\\Sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="4e739-127">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="4e739-128">Dies geschieht normalerweise, wenn das .NET Core SDK auf einem Computer mit Kopieren/Einfügen, anstatt das MSI-Installationsprogramm bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="4e739-128">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="4e739-129">Deinstallieren Sie alle 32-Bit .NET Core SDKs und Laufzeiten um diese Warnung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="4e739-129">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="4e739-130">Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**.</span><span class="sxs-lookup"><span data-stu-id="4e739-130">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4e739-131">Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="4e739-131">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="4e739-132">Es wurden keine .NET Core SDKs erkannt.</span><span class="sxs-lookup"><span data-stu-id="4e739-132">No .NET Core SDKs were detected</span></span>

<span data-ttu-id="4e739-133">In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4e739-133">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4e739-134">Es wurden keine .NET Core SDKs erkannt, stellen Sie sicher, dass sie in der Umgebungsvariablen "PATH" enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="4e739-134">No .NET Core SDKs were detected, ensure they are included in the environment variable 'PATH'.</span></span>

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/NoNetCore.png)

<span data-ttu-id="4e739-136">Diese Warnung wird angezeigt, wenn die Umgebungsvariable `PATH` verweist nicht auf alle .NET Core-SDKs auf dem Computer (z. B. `C:\Program Files\dotnet\` und `C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="4e739-136">This warning appears when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine (for example, `C:\Program Files\dotnet\` and `C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="4e739-137">Um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="4e739-137">To resolve this problem:</span></span>

* <span data-ttu-id="4e739-138">Installieren, oder stellen Sie sicher, dass das .NET Core SDK installiert ist.</span><span class="sxs-lookup"><span data-stu-id="4e739-138">Install or verify the .NET Core SDK is installed.</span></span> <span data-ttu-id="4e739-139">Abrufen der neuesten Installation von [.NET Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="4e739-139">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span> 
* <span data-ttu-id="4e739-140">Überprüfen Sie, ob die `PATH` Umgebungsvariable verweist auf den Speicherort, in dem das SDK installiert ist.</span><span class="sxs-lookup"><span data-stu-id="4e739-140">Verify that the `PATH` environment variable points to the location where the SDK is installed.</span></span> <span data-ttu-id="4e739-141">Der Installer normalerweise legt der `PATH`.</span><span class="sxs-lookup"><span data-stu-id="4e739-141">The installer normally sets the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="4e739-142">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="4e739-142">Obtain data from an app</span></span>

<span data-ttu-id="4e739-143">Wenn eine app auf Anforderungen reagiert werden kann, können Sie die folgenden Daten aus der app unter Verwendung von Middleware abrufen:</span><span class="sxs-lookup"><span data-stu-id="4e739-143">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="4e739-144">Anforderung &ndash; Methode, Schema, Host, Pathbase, Pfad, Abfragezeichenfolge, Header</span><span class="sxs-lookup"><span data-stu-id="4e739-144">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="4e739-145">Verbindung &ndash; Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Client-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="4e739-145">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="4e739-146">Identität &ndash; Namen, Anzeigenamen</span><span class="sxs-lookup"><span data-stu-id="4e739-146">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="4e739-147">Konfigurationseinstellungen</span><span class="sxs-lookup"><span data-stu-id="4e739-147">Configuration settings</span></span>
* <span data-ttu-id="4e739-148">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4e739-148">Environment variables</span></span>

<span data-ttu-id="4e739-149">Fügen Sie Folgendes [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) Code am Anfang der `Startup.Configure` Pipeline zur anforderungsverarbeitung-Methode.</span><span class="sxs-lookup"><span data-stu-id="4e739-149">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="4e739-150">Die Umgebung wird überprüft, bevor die Middleware ausgeführt wird, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4e739-150">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="4e739-151">Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="4e739-151">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="4e739-152">Einfügen der `IHostingEnvironment` in die `Startup.Configure` -Methode, und überprüfen Sie die Umgebung mit der lokalen Variablen.</span><span class="sxs-lookup"><span data-stu-id="4e739-152">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="4e739-153">Der folgende Code veranschaulicht diesen Ansatz.</span><span class="sxs-lookup"><span data-stu-id="4e739-153">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="4e739-154">Weisen Sie die Umgebung zu einer Eigenschaft in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="4e739-154">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="4e739-155">Überprüfen Sie die Umgebung, die mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="4e739-155">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
