---
title: Problembehandlung bei ASP.NET Core-Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: c8b34f51fd329eb9a7c34f7be93bd7f2aa054283
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899286"
---
# <a name="troubleshoot-aspnet-core-projects"></a>Problembehandlung bei ASP.NET Core-Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Die folgenden Links erhalten Anleitungen zur Fehlerbehebung:

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET-Blog: Behandlung von Leistungsproblemen von ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK-Warnungen

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Die 32-Bit und 64-Bit-Versionen von .NET Core SDK sind installiert.

In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:

> Es werden sowohl 32- und 64-Bit-Versionen von .NET Core SDK installiert. Nur Vorlagen aus die 64-Bit-Versionen installiert ' "c:"\\Programmdateien\\Dotnet\\Sdk\\"wird angezeigt.

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/both32and64bit.png)

Diese Warnung wird angezeigt, wenn sowohl die 32-Bit-(x86) als auch die 64-Bit (x 64) Versionen der [.NET Core SDK](https://www.microsoft.com/net/download/all) installiert sind. Häufige Gründe, die beide Versionen installiert sind enthalten:

* Sie ursprünglich heruntergeladen das .NET Core SDK-Installationsprogramm, das mit einem 32-Bit-Computer, aber klicken Sie dann über kopiert und es auf einem 64-Bit-Computer installiert.
* Das 32-Bit .NET Core SDK wurde durch eine andere Anwendung installiert.
* Die falsche Version wurde heruntergeladen und installiert.

Deinstallieren Sie die 32-Bit .NET Core-SDK, um diese Warnung zu vermeiden. Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**. Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Das .NET Core SDK ist an mehreren Speicherorten installiert.

In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:

> Das .NET Core SDK ist an mehreren Speicherorten installiert. Nur Vorlagen aus dem SDK unter "C:\\Programmdateien\\Dotnet\\Sdk\\" wird angezeigt.

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/multiplelocations.png)

Diese Meldung angezeigt, wenn Sie mindestens eine Installation von .NET Core SDK in einem Verzeichnis außerhalb des haben *C:\\Programmdateien\\Dotnet\\Sdk\\*. Dies geschieht normalerweise, wenn das .NET Core SDK auf einem Computer mit Kopieren/Einfügen, anstatt das MSI-Installationsprogramm bereitgestellt wurde.

Deinstallieren Sie die 32-Bit .NET Core-SDK, um diese Warnung zu vermeiden. Deinstallieren von **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder Ändern eines Programms**. Wenn Sie verstehen, warum die Warnung tritt auf, und deren Auswirkungen, können Sie die Warnung ignorieren.

### <a name="no-net-core-sdks-were-detected"></a>Es wurden keine .NET Core SDKs erkannt.

In der **neues Projekt** Dialogfeld für ASP.NET Core, können Sie die folgende Warnung angezeigt:

> Es wurden keine .NET Core SDKs erkannt, stellen Sie sicher, dass sie in der Umgebungsvariablen "PATH" enthalten sind.

![Screenshot des Dialogfelds mit die Warnmeldung OneASP.NET](troubleshoot/_static/NoNetCore.png)

Diese Warnung wird angezeigt, wenn die Umgebungsvariable `PATH` verweist nicht auf alle .NET Core-SDKs auf dem Computer (z. B. `C:\Program Files\dotnet\` und `C:\Program Files (x86)\dotnet\`). Um dieses Problem zu beheben:

* Installieren, oder stellen Sie sicher, dass das .NET Core SDK installiert ist. Abrufen der neuesten Installation von [.NET Downloads](https://dotnet.microsoft.com/download). 
* Überprüfen Sie, ob die `PATH` Umgebungsvariable verweist auf den Speicherort, in dem das SDK installiert ist. Der Installer normalerweise legt der `PATH`.

## <a name="obtain-data-from-an-app"></a>Abrufen von Daten aus einer App

Wenn eine app auf Anforderungen reagiert werden kann, können Sie die folgenden Daten aus der app unter Verwendung von Middleware abrufen:

* Anforderung &ndash; Methode, Schema, Host, Pathbase, Pfad, Abfragezeichenfolge, Header
* Verbindung &ndash; Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Client-Zertifikat
* Identität &ndash; Namen, Anzeigenamen
* Konfigurationseinstellungen
* Umgebungsvariablen

Fügen Sie Folgendes [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) Code am Anfang der `Startup.Configure` Pipeline zur anforderungsverarbeitung-Methode. Die Umgebung wird überprüft, bevor die Middleware ausgeführt wird, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.

Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:

* Einfügen der `IHostingEnvironment` in die `Startup.Configure` -Methode, und überprüfen Sie die Umgebung mit der lokalen Variablen. Der folgende Code veranschaulicht diesen Ansatz.

* Weisen Sie die Umgebung zu einer Eigenschaft in der `Startup` Klasse. Überprüfen Sie die Umgebung, die mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).

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
