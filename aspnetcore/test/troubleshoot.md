---
title: Problembehandlung und Debuggen von ASP.net Core Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 73a73fb51571e5f7b706ff4b958217854750c1fb
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354711"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Problembehandlung und Debuggen von ASP.net Core Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Die folgenden Links enthalten Anleitungen zur Problembehandlung:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC-Konferenz (London, 2018): Diagnostizieren von Problemen in ASP.net Core Anwendungen](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.net-Blog: Problembehandlung bei der ASP.net Core von Leistungsproblemen](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.Net Core SDK Warnungen

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Die 32-Bit-und 64-Bit-Versionen der .net Core SDK werden installiert.

Im Dialogfeld " **Neues Projekt** " für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:

> Sowohl 32-Bit-als auch 64-Bit-Versionen der .net Core SDK werden installiert. Nur Vorlagen aus den 64-Bit-Versionen, die unter ' C:\\Programme\\dotnet\\SDK\\' installiert sind, werden angezeigt.

Diese Warnung wird angezeigt, wenn sowohl die Versionen 32-Bit (x86) als auch 64-Bit (x64) der [.net Core SDK](https://www.microsoft.com/net/download/all) installiert sind. Häufige Gründe für die Installation beider Versionen sind u. a.:

* Sie haben ursprünglich den .net Core SDK Installer mithilfe eines 32-Bit-Computers heruntergeladen, ihn jedoch auf einen 64-Bit-Computer kopiert und dort installiert.
* Der 32-Bit-.net Core SDK wurde von einer anderen Anwendung installiert.
* Die falsche Version wurde heruntergeladen und installiert.

Deinstallieren Sie die 32-Bit-.net Core SDK, um diese Warnung zu vermeiden. Deinstallieren Sie in der **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder ändern Sie ein Programm**. Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Die .net Core SDK wird an mehreren Speicherorten installiert.

Im Dialogfeld " **Neues Projekt** " für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:

> Der .net Core SDK wird an mehreren Speicherorten installiert. Es werden nur Vorlagen aus den SDKs angezeigt, die unter "C:\\Programme\\dotnet\\SDK\\" installiert sind.

Diese Meldung wird angezeigt, wenn mindestens eine Installation des .net Core SDK in einem Verzeichnis außerhalb von *C:\\Programme\\dotnet\\SDK\\* vorhanden ist. Dies geschieht normalerweise, wenn die .net Core SDK auf einem Computer mithilfe von Kopieren/Einfügen anstelle des MSI-Installers bereitgestellt wurde.

Deinstallieren Sie alle 32-Bit .net Core sdchs und Laufzeiten, um diese Warnung zu vermeiden. Deinstallieren Sie in der **Systemsteuerung** > **Programme und Funktionen** > **deinstallieren oder ändern Sie ein Programm**. Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.

### <a name="no-net-core-sdks-were-detected"></a>Es wurden keine .net Core-sdche erkannt.

* Im Dialogfeld " **Neues Projekt** " in Visual Studio für ASP.net Core wird möglicherweise die folgende Warnung angezeigt:

  > Es wurden keine .net Core sdert erkannt. Stellen Sie sicher, dass Sie in der Umgebungsvariablen `PATH`enthalten sind.

* Wenn Sie einen `dotnet` Befehl ausführen, wird die Warnung wie folgt angezeigt:

  > Es war nicht möglich, installierte dotnet-sdche zu finden.

Diese Warnungen werden angezeigt, wenn die Umgebungsvariable `PATH` nicht auf .net Core-sdche auf dem Computer verweist. So beheben Sie dieses Problem

* Installieren Sie das .NET Core-SDK. Holen Sie sich den neuesten Installer über [.net-Downloads](https://dotnet.microsoft.com/download).
* Vergewissern Sie sich, dass die `PATH`-Umgebungsvariable auf den Speicherort verweist, an dem das SDK installiert ist (`C:\Program Files\dotnet\` für 64-Bit/x64 oder `C:\Program Files (x86)\dotnet\` für 32-Bit/x86). Der SDK-Installer legt normalerweise die `PATH`fest. Installieren Sie immer die gleichen bitangaben und-Laufzeiten auf dem gleichen Computer.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Fehlendes SDK nach Installation des .net Core-hostingpakets

Durch die Installation des [.net Core-hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) werden die `PATH` geändert, wenn die .net Core-Laufzeit installiert wird, um auf die 32-Bit-Version (x86) von .net Core (`C:\Program Files (x86)\dotnet\`) zu verweisen. Dies kann zu fehlenden sdten führen, wenn der .net Core-`dotnet` Befehl (x86) von 32 Bit verwendet wird (es[wurden keine .net Core sdert erkannt](#no-net-core-sdks-were-detected)). Um dieses Problem zu beheben, verschieben Sie `C:\Program Files\dotnet\` an eine Position vor `C:\Program Files (x86)\dotnet\` auf dem `PATH`.

## <a name="obtain-data-from-an-app"></a>Abrufen von Daten aus einer App

Wenn eine APP auf Anforderungen reagieren kann, können Sie die folgenden Daten über die Middleware von der App abrufen:

* Anforderungs &ndash; Methode, Schema, Host, pathbase, Pfad, Abfrage Zeichenfolge, Header
* Verbindung &ndash; Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Client Zertifikat
* Identität &ndash; Name, Anzeige Name
* Konfigurationseinstellungen
* Umgebungsvariablen

Platzieren Sie den folgenden [middlewarescode](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) am Anfang der Pipeline für die Anforderungs Verarbeitung der `Startup.Configure` Methode. Die Umgebung wird vor der Ausführung der Middleware geprüft, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.

Verwenden Sie zum Abrufen der Umgebung einen der folgenden Ansätze:

* Fügen Sie die `IHostingEnvironment` in die `Startup.Configure`-Methode ein, und überprüfen Sie die Umgebung mit der lokalen Variablen. Der folgende Beispielcode veranschaulicht diese Vorgehensweise.

* Weisen Sie die Umgebung einer Eigenschaft in der `Startup`-Klasse zu. Überprüfen Sie die Umgebung mithilfe der-Eigenschaft (z. b. `if (Environment.IsDevelopment())`).

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

## <a name="debug-aspnet-core-apps"></a>Debuggen ASP.net Core apps

Die folgenden Links enthalten Informationen zum Debuggen von ASP.net Core-apps.

* [Debuggen von ASP Core unter Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debuggen von .net Core unter UNIX über SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Schnellstart: Debuggen von ASP.net mit dem Visual Studio-Debugger](/visualstudio/debugger/quickstart-debug-aspnet)
* Weitere Informationen zum Debuggen finden Sie in [diesem GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/2960) .
