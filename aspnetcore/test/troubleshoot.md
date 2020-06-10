---
title: 'title: author: description: ms.author: ms.custom: ms.date: no-loc:'
author: Rick-Anderson
description: "'Blazor'"
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/troubleshoot
ms.openlocfilehash: bd5e74569717db26496760eaaa2760122d4b49e7
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106493"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="b81eb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b81eb-103">'Identity'</span></span>

<span data-ttu-id="b81eb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b81eb-104">'Let's Encrypt'</span></span>

<span data-ttu-id="b81eb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b81eb-105">'Razor'</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <span data-ttu-id="b81eb-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b81eb-106">'SignalR' uid:</span></span>
* <span data-ttu-id="b81eb-107">Problembehandlung und Debuggen von ASP.NET Core-Projekten</span><span class="sxs-lookup"><span data-stu-id="b81eb-107">Troubleshoot and debug ASP.NET Core projects</span></span>

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="b81eb-108">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b81eb-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="b81eb-109">Die folgenden Links bieten Hinweise zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="b81eb-109">The following links provide troubleshooting guidance:</span></span>

[<span data-ttu-id="b81eb-110">NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="b81eb-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)

> [<span data-ttu-id="b81eb-111">ASP.NET-Blog: Problembehandlung bei ASP. NET Core-Leistungsproblemen</span><span class="sxs-lookup"><span data-stu-id="b81eb-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/) <span data-ttu-id="b81eb-112">.NET Core SDK-Warnungen</span><span class="sxs-lookup"><span data-stu-id="b81eb-112">.NET Core SDK warnings</span></span>

<span data-ttu-id="b81eb-113">Sowohl die 32-Bit- als auch die 64-Bit-Version des .NET Core SDKs sind installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span> <span data-ttu-id="b81eb-114">Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b81eb-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

* <span data-ttu-id="b81eb-115">Sowohl 32-Bit- als auch 64-Bit-Versionen des .NET Core SDKs sind installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span>
* <span data-ttu-id="b81eb-116">Es werden nur Vorlagen der 64-Bit-Versionen angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.</span><span class="sxs-lookup"><span data-stu-id="b81eb-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>
* <span data-ttu-id="b81eb-117">Diese Warnung wird angezeigt, wenn sowohl 32-Bit- (x86) als auch 64-Bit-Versionen (x64) des [.NET Core SDKs](https://dotnet.microsoft.com/download/dotnet-core) installiert sind.</span><span class="sxs-lookup"><span data-stu-id="b81eb-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span>

<span data-ttu-id="b81eb-118">Häufige Gründe für die Installation beider Versionen sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="b81eb-118">Common reasons both versions may be installed include:</span></span> <span data-ttu-id="b81eb-119">Sie haben das .NET Core SDK-Installationsprogramm ursprünglich mit einem 32-Bit-Computer heruntergeladen, es dann aber auf einen 64-Bit-Computer kopiert und installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span> <span data-ttu-id="b81eb-120">Die 32-Bit-Version des .NET Core SDKs wurde von einer anderen Anwendung installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-120">The 32-bit .NET Core SDK was installed by another application.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="b81eb-121">Die falsche Version wurde heruntergeladen und installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="b81eb-122">Deinstallieren Sie die 32-Bit-Version des .NET Core SDKs, um diese Warnung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b81eb-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span>

> <span data-ttu-id="b81eb-123">Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**.</span><span class="sxs-lookup"><span data-stu-id="b81eb-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="b81eb-124">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="b81eb-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

<span data-ttu-id="b81eb-125">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-125">The .NET Core SDK is installed in multiple locations</span></span> <span data-ttu-id="b81eb-126">Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b81eb-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

<span data-ttu-id="b81eb-127">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="b81eb-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="b81eb-128">Es werden nur Vorlagen des SDKs angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.</span><span class="sxs-lookup"><span data-stu-id="b81eb-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span> <span data-ttu-id="b81eb-129">Diese Meldung wird angezeigt, wenn Sie über mindestens eine Installation des .NET Core SDK in einem Verzeichnis außerhalb von *C:\\Programme\\dotnet\\sdk\\* verfügen.</span><span class="sxs-lookup"><span data-stu-id="b81eb-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="b81eb-130">Normalerweise geschieht dies, wenn das .NET Core SDKs auf einem Computer über Kopieren/Einfügen anstatt mit dem MSI-Installationsprogramm bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b81eb-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

* <span data-ttu-id="b81eb-131">Deinstallieren Sie alle 32-Bit-Versionen des .NET Core SDKs und der Laufzeiten, um diese Warnung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="b81eb-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span>

  > <span data-ttu-id="b81eb-132">Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**.</span><span class="sxs-lookup"><span data-stu-id="b81eb-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span>

* <span data-ttu-id="b81eb-133">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="b81eb-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

  > <span data-ttu-id="b81eb-134">Es wurden keine .NET Core SDKs erkannt.</span><span class="sxs-lookup"><span data-stu-id="b81eb-134">No .NET Core SDKs were detected</span></span>

<span data-ttu-id="b81eb-135">Im Dialogfeld **Neues Projekt** in Visual Studio für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b81eb-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span> <span data-ttu-id="b81eb-136">Es wurden keine .NET Core SDKs erkannt. Stellen Sie sicher, dass sie in der Umgebungsvariablen `PATH` enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b81eb-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="b81eb-137">Wenn Sie einen `dotnet`-Befehl ausführen, wird die Meldung wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="b81eb-137">When executing a `dotnet` command, the warning appears as:</span></span> <span data-ttu-id="b81eb-138">Es konnten keine installierten Dotnet-SDKs gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="b81eb-138">It was not possible to find any installed dotnet SDKs.</span></span>
* <span data-ttu-id="b81eb-139">Diese Warnungen werden angezeigt, wenn die Umgebungsvariable `PATH` nicht auf . NET Core SDKs auf dem Computer verweist.</span><span class="sxs-lookup"><span data-stu-id="b81eb-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="b81eb-140">Dieses Problem lässt sich wie folgt beheben:</span><span class="sxs-lookup"><span data-stu-id="b81eb-140">To resolve this problem:</span></span> <span data-ttu-id="b81eb-141">Installieren Sie das .NET Core-SDK.</span><span class="sxs-lookup"><span data-stu-id="b81eb-141">Install the .NET Core SDK.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="b81eb-142">Das aktuellste Installationsprogramm erhalten Sie unter [.NET-Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="b81eb-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="b81eb-143">Stellen Sie sicher, dass die Umgebungsvariable `PATH` auf den Speicherort verweist, an dem das SDK installiert ist (`C:\Program Files\dotnet\` für 64-Bit/x64 oder `C:\Program Files (x86)\dotnet\` für 32-Bit/x86).</span><span class="sxs-lookup"><span data-stu-id="b81eb-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="b81eb-144">Das SDK-Installationsprogramm legt normalerweise den `PATH` fest.</span><span class="sxs-lookup"><span data-stu-id="b81eb-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="b81eb-145">Installieren Sie immer SDKs und Laufzeiten mit derselben Bitanzahl auf demselben Computer.</span><span class="sxs-lookup"><span data-stu-id="b81eb-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="b81eb-146">Fehlendes SDK nach der Installation des .NET Core-Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="b81eb-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="b81eb-147">Durch die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) wird der `PATH` bei der Installation der .NET Core-Laufzeit so geändert, dass er auf die 32-Bit-Version (x86) von .NET Core (`C:\Program Files (x86)\dotnet\`) verweist.</span><span class="sxs-lookup"><span data-stu-id="b81eb-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span>

* <span data-ttu-id="b81eb-148">Dies kann zu fehlenden SDKs führen, wenn der 32-Bit (x86) .NET Core-Befehl `dotnet` verwendet wird ([Es wurden keine .NET Core SDKs erkannt](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="b81eb-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span>
* <span data-ttu-id="b81eb-149">Um dieses Problem zu beheben, verschieben Sie `C:\Program Files\dotnet\` an einen Speicherort vor `C:\Program Files (x86)\dotnet\` in dem `PATH`.</span><span class="sxs-lookup"><span data-stu-id="b81eb-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>
* Identity: Name, display name
* <span data-ttu-id="b81eb-150">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="b81eb-150">Obtain data from an app</span></span>
* <span data-ttu-id="b81eb-151">Wenn eine App in der Lage ist, auf Anforderungen zu antworten, können Sie die folgenden Daten über eine Middleware aus der App erhalten:</span><span class="sxs-lookup"><span data-stu-id="b81eb-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

<span data-ttu-id="b81eb-152">Anforderung: Methode, Schema, Host, Pfadbasis, Pfad, Abfragezeichenfolge, Header</span><span class="sxs-lookup"><span data-stu-id="b81eb-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span> <span data-ttu-id="b81eb-153">Verbindung: Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Clientzertifikat</span><span class="sxs-lookup"><span data-stu-id="b81eb-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>

<span data-ttu-id="b81eb-154">Konfigurationseinstellungen</span><span class="sxs-lookup"><span data-stu-id="b81eb-154">Configuration settings</span></span>

* <span data-ttu-id="b81eb-155">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="b81eb-155">Environment variables</span></span> <span data-ttu-id="b81eb-156">Platzieren Sie den folgenden [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)-Code an den Anfang der Anforderungsverarbeitungspipeline der `Startup.Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="b81eb-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span>

* <span data-ttu-id="b81eb-157">Die Umgebung wird vor der Ausführung der Middleware überprüft, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b81eb-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span> <span data-ttu-id="b81eb-158">Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="b81eb-158">To obtain the environment, use either of the following approaches:</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="b81eb-159">Fügen Sie die `IHostingEnvironment` in die `Startup.Configure`-Methode ein, und prüfen Sie die Umgebung mit der lokalen Variablen.</span><span class="sxs-lookup"><span data-stu-id="b81eb-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span>

<span data-ttu-id="b81eb-160">Dieser Ansatz wird im folgenden Beispielcode veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="b81eb-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="b81eb-161">Weisen Sie die Umgebung einer Eigenschaft der Klasse `Startup` zu.</span><span class="sxs-lookup"><span data-stu-id="b81eb-161">Assign the environment to a property in the `Startup` class.</span></span>
* <span data-ttu-id="b81eb-162">Überprüfen Sie die Umgebung mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="b81eb-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>
* <span data-ttu-id="b81eb-163">Debuggen von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="b81eb-163">Debug ASP.NET Core apps</span></span>
* <span data-ttu-id="b81eb-164">Die folgenden Links bieten Informationen zum Debuggen von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="b81eb-164">The following links provide information on debugging ASP.NET Core apps.</span></span>
