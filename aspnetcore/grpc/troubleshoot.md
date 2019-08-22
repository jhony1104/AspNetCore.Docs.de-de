---
title: Problembehandlung bei GrpC auf .net Core
author: jamesnk
description: Beheben von Fehlern bei der Verwendung von GrpC auf .net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/17/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 7621266dfe26b7126d1607e195dd5dcaab4efa55
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886491"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="d6449-103">Problembehandlung bei GrpC auf .net Core</span><span class="sxs-lookup"><span data-stu-id="d6449-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="d6449-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d6449-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="d6449-105">Keine Übereinstimmung zwischen Client-und Dienst-SSL/TLS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d6449-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="d6449-106">Die GrpC-Vorlage und-Beispiele verwenden [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) , um GrpC-Dienste standardmäßig zu sichern.</span><span class="sxs-lookup"><span data-stu-id="d6449-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="d6449-107">GrpC-Clients müssen eine sichere Verbindung verwenden, um gesicherte GrpC-Dienste erfolgreich aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d6449-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="d6449-108">Sie können überprüfen, ob der ASP.net Core GrpC-Dienst TLS in den in App-Start geschriebenen Protokollen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d6449-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="d6449-109">Der Dienst lauscht an einem HTTPS-Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="d6449-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="d6449-110">Der .net Core-Client muss `https` in der Server Adresse verwenden, um Aufrufe mit einer gesicherten Verbindung durchführen zu können:</span><span class="sxs-lookup"><span data-stu-id="d6449-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="d6449-111">Alle GrpC-Client Implementierungen unterstützen TLS.</span><span class="sxs-lookup"><span data-stu-id="d6449-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="d6449-112">für GrpC-Clients aus anderen Sprachen ist in der Regel `SslCredentials`der mit konfigurierte Kanal erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d6449-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="d6449-113">`SslCredentials`Gibt das Zertifikat an, das vom Client verwendet wird, und es muss anstelle unsicherer Anmelde Informationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="d6449-114">Beispiele für das Konfigurieren der verschiedenen GrpC-Client Implementierungen für die Verwendung von TLS finden Sie unter [GrpC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="d6449-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="d6449-115">Anrufen unsicherer GrpC-Dienste mit .net Core-Client</span><span class="sxs-lookup"><span data-stu-id="d6449-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="d6449-116">Eine zusätzliche Konfiguration ist erforderlich, um unsichere GrpC-Dienste mit dem .net Core-Client aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d6449-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="d6449-117">Der GrpC-Client muss den `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` Switch auf `true` festlegen und `http` in der Server Adresse verwenden:</span><span class="sxs-lookup"><span data-stu-id="d6449-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="d6449-118">ASP.net Core GrpC-app unter macOS konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="d6449-119">Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht.</span><span class="sxs-lookup"><span data-stu-id="d6449-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="d6449-120">Die ASP.net Core GrpC-Vorlage und-Beispiele verwenden standardmäßig TLS.</span><span class="sxs-lookup"><span data-stu-id="d6449-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="d6449-121">Wenn Sie versuchen, den GrpC-Server zu starten, wird die folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d6449-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="d6449-122">Bindung https://localhost:5001 an an der IPv4-Loopback Schnittstelle nicht möglich: HTTP/2 über TLS wird unter macOS aufgrund fehlender alpn-Unterstützung nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d6449-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="d6449-123">Um dieses Problem zu umgehen, konfigurieren Sie Kestrel und den GrpC-Client für die Verwendung von http/2 *ohne* TLS.</span><span class="sxs-lookup"><span data-stu-id="d6449-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="d6449-124">Dies sollte nur während der Entwicklung durchzuführen sein.</span><span class="sxs-lookup"><span data-stu-id="d6449-124">You should only do this during development.</span></span> <span data-ttu-id="d6449-125">Die Verwendung von TLS führt dazu, dass GrpC-Nachrichten ohne Verschlüsselung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="d6449-126">Kestrel muss einen http/2-Endpunkt ohne TLS in *Program.cs*konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d6449-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d6449-127">Wenn ein http/2-Endpunkt ohne TLS konfiguriert ist, müssen die [listenoptions.-Protokolle](xref:fundamentals/servers/kestrel#listenoptionsprotocols) des Endpunkts `HttpProtocols.Http2`auf festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-127">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="d6449-128">`HttpProtocols.Http1AndHttp2`kann nicht verwendet werden, da TLS zum Aushandeln von http/2 erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="d6449-128">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="d6449-129">Ohne TLS werden alle Verbindungen mit dem Endpunkt standardmäßig auf HTTP/1.1, und GrpC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="d6449-129">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="d6449-130">Der GrpC-Client muss auch für die Verwendung von TLS konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-130">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="d6449-131">Weitere Informationen finden Sie unter [callunsichere GrpC-Dienste mit .net Core-Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="d6449-131">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="d6449-132">HTTP/2 ohne TLS sollte nur während der APP-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-132">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="d6449-133">Produktions-apps sollten immer Transportsicherheit verwenden.</span><span class="sxs-lookup"><span data-stu-id="d6449-133">Production apps should always use transport security.</span></span> <span data-ttu-id="d6449-134">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in GrpC für ASP.net Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="d6449-134">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="d6449-135">GrpC C# -Assets werden nicht aus  *\*. proto* -Dateien generiert.</span><span class="sxs-lookup"><span data-stu-id="d6449-135">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="d6449-136">die GrpC-Codegenerierung von konkreten Clients und Dienst Basisklassen erfordert, dass protobuf-Dateien und-Tools von einem Projekt referenziert werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-136">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="d6449-137">Sie müssen Folgendes einschließen:</span><span class="sxs-lookup"><span data-stu-id="d6449-137">You must include:</span></span>

* <span data-ttu-id="d6449-138">*. proto* -Dateien, die Sie in der `<Protobuf>` Element Gruppe verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="d6449-138">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="d6449-139">Auf [importierte *. proto* -Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss vom Projekt verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="d6449-139">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="d6449-140">Paket Verweis auf das GrpC-Toolpaket " [GrpC. Tools](https://www.nuget.org/packages/Grpc.Tools/)".</span><span class="sxs-lookup"><span data-stu-id="d6449-140">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="d6449-141">Weitere Informationen zum Erstellen von GrpC C# -Assets finden <xref:grpc/basics>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="d6449-141">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="d6449-142">Standardmäßig generiert ein `<Protobuf>` Verweis einen konkreten Client und eine Dienst Basisklasse.</span><span class="sxs-lookup"><span data-stu-id="d6449-142">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="d6449-143">Das-Attribut des `GrpcServices` Reference-Elements kann verwendet werden C# , um die Medienobjekt Generierung einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="d6449-143">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="d6449-144">Gültige `GrpcServices` Optionen sind:</span><span class="sxs-lookup"><span data-stu-id="d6449-144">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="d6449-145">`Both`(Standardwert, wenn nicht vorhanden)</span><span class="sxs-lookup"><span data-stu-id="d6449-145">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="d6449-146">Eine ASP.net Core Web-App, die GrpC-Dienste gehostet, benötigt nur die generierte Dienst Basisklasse:</span><span class="sxs-lookup"><span data-stu-id="d6449-146">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="d6449-147">Für eine GrpC-Client-App, die GrpC-Anrufe erstellt, muss nur der konkrete Client generiert werden</span><span class="sxs-lookup"><span data-stu-id="d6449-147">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
