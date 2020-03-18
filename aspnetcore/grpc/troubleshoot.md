---
title: Problembehandlung für gRPC in .NET Core
author: jamesnk
description: Beheben Sie Fehler bei der Verwendung von gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 10/16/2019
uid: grpc/troubleshoot
ms.openlocfilehash: c501cda14f3bac9297695ece59cbc4634e4b7895
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649363"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="6fd21-103">Problembehandlung für gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="6fd21-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="6fd21-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6fd21-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6fd21-105">In diesem Dokument werden häufig auftretende Probleme bei der Entwicklung von gRPC-Apps in .NET behandelt.</span><span class="sxs-lookup"><span data-stu-id="6fd21-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="6fd21-106">Nichtübereinstimmung der SSL-/TLS-Konfiguration von Client und Dienst</span><span class="sxs-lookup"><span data-stu-id="6fd21-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="6fd21-107">In der gRPC-Vorlage und den gRPC-Beispielen werden gRPC-Dienste standardmäßig durch [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert.</span><span class="sxs-lookup"><span data-stu-id="6fd21-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="6fd21-108">gRPC-Clients müssen eine sichere Verbindung verwenden, um gesicherte gRPC-Dienste aufzurufen zu können.</span><span class="sxs-lookup"><span data-stu-id="6fd21-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="6fd21-109">Sie können in den Protokollen, die beim Starten der App geschrieben werden, überprüfen, ob ein ASP.NET Core-gRPC-Dienst TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="6fd21-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="6fd21-110">Der Dienst lauscht in diesem Fall auf einen HTTPS-Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="6fd21-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="6fd21-111">Der .NET Core-Client muss in der Serveradresse `https` verwenden, um Aufrufe über eine sichere Verbindung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="6fd21-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="6fd21-112">Alle gRPC-Clientimplementierungen unterstützen TLS.</span><span class="sxs-lookup"><span data-stu-id="6fd21-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="6fd21-113">Bei gRPC-Clients mit anderen Sprachen ist üblicherweise ein mit `SslCredentials` konfigurierter Kanal erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6fd21-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="6fd21-114">`SslCredentials` gibt das Zertifikat an, das der Client verwendet, und muss anstelle von unsicheren Anmeldeinformationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="6fd21-115">Beispiele für das Konfigurieren der verschiedenen gRPC-Clientimplementierungen für die Verwendung von TLS finden Sie unter [gRPC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="6fd21-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="6fd21-116">Aufrufen eines gRPC-Diensts mit einem nicht vertrauenswürdigen/ungültigen Zertifikat</span><span class="sxs-lookup"><span data-stu-id="6fd21-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="6fd21-117">Der .NET-gRPC-Client erfordert, dass der Dienst über ein vertrauenswürdiges Zertifikat verfügt.</span><span class="sxs-lookup"><span data-stu-id="6fd21-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="6fd21-118">Die folgende Fehlermeldung wird zurückgegeben, wenn ein gRPC-Dienst ohne vertrauenswürdiges Zertifikat aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="6fd21-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="6fd21-119">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="6fd21-119">Unhandled exception.</span></span> <span data-ttu-id="6fd21-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span><span class="sxs-lookup"><span data-stu-id="6fd21-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="6fd21-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure. (Ausnahmefehler. System.Net.Http.HttpRequestException: Es konnte keine SSL-Verbindung hergestellt werden, siehe interne Ausnahme. ---> System.Security.Authentication.AuthenticationException: Das Remotezertifikat ist laut Validierungsverfahren ungültig.)</span><span class="sxs-lookup"><span data-stu-id="6fd21-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="6fd21-122">Dieser Fehler wird möglicherweise angezeigt, wenn Sie Ihre App lokal testen und das ASP.NET Core-HTTPS-Entwicklungszertifikat nicht vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="6fd21-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="6fd21-123">Anweisungen zum Beheben dieses Problems finden Sie unter [Einstufen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="6fd21-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="6fd21-124">Wenn Sie einen gRPC-Dienst auf einem anderen Computer aufrufen und das Zertifikat nicht als vertrauenswürdig einstufen können, kann der gRPC-Client so konfiguriert werden, dass er das ungültige Zertifikat ignoriert.</span><span class="sxs-lookup"><span data-stu-id="6fd21-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="6fd21-125">Im folgenden Code wird [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) verwendet, um Aufrufe ohne vertrauenswürdiges Zertifikat zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="6fd21-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;
var httpClient = new HttpClient(httpClientHandler);

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpClient = httpClient });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="6fd21-126">Nicht vertrauenswürdige Zertifikate sollten nur während der App-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="6fd21-127">Produktions-Apps sollten stets gültige Zertifikate verwenden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="6fd21-128">Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client</span><span class="sxs-lookup"><span data-stu-id="6fd21-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="6fd21-129">Wenn Sie unsichere gRPC-Dienste mit dem .NET Core-Client aufrufen möchten, sind weitere Konfigurationen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6fd21-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="6fd21-130">Der gRPC-Client muss für den Parameter `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` `true` festlegen und in der Serveradresse `http` verwenden:</span><span class="sxs-lookup"><span data-stu-id="6fd21-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="6fd21-131">ASP.NET Core-gRPC-App kann unter macOS nicht gestartet werden</span><span class="sxs-lookup"><span data-stu-id="6fd21-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="6fd21-132">Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht.</span><span class="sxs-lookup"><span data-stu-id="6fd21-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="6fd21-133">In der ASP.NET Core-gRPC-Vorlage und den ASP.NET Core-gRPC-Beispielen wird standardmäßig TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="6fd21-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="6fd21-134">Wenn Sie versuchen, den gRPC-Server zu starten, wird folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fd21-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="6fd21-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'. (Eine Bindung an „https://localhost:5001“ auf der IPv4-Loopback-Schnittstelle kann nicht erfolgen: „HTTP/2 über TLS wird unter macOS aufgrund von fehlender ALPN-Unterstützung nicht unterstützt.“)</span><span class="sxs-lookup"><span data-stu-id="6fd21-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="6fd21-136">Konfigurieren Sie Kestrel und den gRPC-Client so, dass HTTP/2 *ohne* TLS verwendet wird, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="6fd21-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="6fd21-137">Dies sollten Sie nur während der Entwicklung tun.</span><span class="sxs-lookup"><span data-stu-id="6fd21-137">You should only do this during development.</span></span> <span data-ttu-id="6fd21-138">Wenn TLS nicht verwendet wird, führt dies dazu, dass gRPC-Nachrichten unverschlüsselt gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="6fd21-139">Kestrel muss einen HTTP/2-Endpunkt ohne TLS in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="6fd21-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="6fd21-140">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="6fd21-141">`HttpProtocols.Http1AndHttp2` kann nicht verwendet werden, da zum Aushandeln von HTTP/2 TLS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="6fd21-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="6fd21-142">Ohne TLS wird für alle Verbindungen zu diesem Endpunkt standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="6fd21-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="6fd21-143">Der gRPC-Client muss ebenfalls so konfiguriert sein, dass er kein TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="6fd21-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="6fd21-144">Weitere Informationen finden Sie im Abschnitt [Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="6fd21-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="6fd21-145">HTTP/2 ohne TLS sollte nur während der App-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="6fd21-146">Produktions-Apps sollten stets Transportsicherheit verwenden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-146">Production apps should always use transport security.</span></span> <span data-ttu-id="6fd21-147">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen zu gRPC für ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="6fd21-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="6fd21-148">Kein Generieren von Code aus PROTO-Dateien bei gRPC-C#-Objekten</span><span class="sxs-lookup"><span data-stu-id="6fd21-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="6fd21-149">Für das Generieren von gRPC-Code für konkrete Clients und Dienstbasisklassen muss in einem Projekt auf Protobuf-Dateien und -Tools verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="6fd21-150">Folgendes muss enthalten sein:</span><span class="sxs-lookup"><span data-stu-id="6fd21-150">You must include:</span></span>

* <span data-ttu-id="6fd21-151">*.proto*-Dateien, die Sie in der Elementgruppe `<Protobuf>` verwenden wollen.</span><span class="sxs-lookup"><span data-stu-id="6fd21-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="6fd21-152">Auf [importierte *.proto*-Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss im Projekt verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="6fd21-153">Paketverweis auf das gRPC-Toolpaket [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="6fd21-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="6fd21-154">Weitere Informationen zum Generieren von gRPC-C#-Objekten finden Sie unter <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="6fd21-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="6fd21-155">Durch einen `<Protobuf>`-Verweis werden standardmäßig ein konkreter Client und eine Dienstbasisklasse generiert.</span><span class="sxs-lookup"><span data-stu-id="6fd21-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="6fd21-156">Mit dem Attribut `GrpcServices` des Verweiselements kann das Generieren von C#-Objekten beschränkt werden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="6fd21-157">Gültige Optionen für `GrpcServices` sind:</span><span class="sxs-lookup"><span data-stu-id="6fd21-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="6fd21-158">`Both` (Standardwert, wenn nicht vorhanden)</span><span class="sxs-lookup"><span data-stu-id="6fd21-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="6fd21-159">Bei einer ASP.NET Core-Web-App, die gRPC-Dienste hostet, muss nur die Dienstbasisklasse generiert werden:</span><span class="sxs-lookup"><span data-stu-id="6fd21-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="6fd21-160">Bei einer gRPC-Client-App, die gRPC-Aufrufe ausführt, muss nur der konkrete Client generiert werden:</span><span class="sxs-lookup"><span data-stu-id="6fd21-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="6fd21-161">Generieren von gRPC-C#-Objekten aus PROTO-Dateien in WPF-Projekten nicht möglich</span><span class="sxs-lookup"><span data-stu-id="6fd21-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="6fd21-162">Es gibt ein [bekanntes Problem](https://github.com/dotnet/wpf/issues/810) bei WPF-Projekten, das verhindert, dass das Generieren von gRPC-Code richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="6fd21-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="6fd21-163">Bei der Verwendung aller gRPC-Typen, die in einem WPF-Projekt durch Verweise auf `Grpc.Tools` und *.proto*-Dateien generiert werden, treten Kompilierungsfehler auf:</span><span class="sxs-lookup"><span data-stu-id="6fd21-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="6fd21-164">Fehler CS0246: Der Typ- oder Namespacename „MyGrpcServices“ wurde nicht gefunden (möglicherweise fehlt eine using-Direktive oder ein Assemblyverweis).</span><span class="sxs-lookup"><span data-stu-id="6fd21-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="6fd21-165">So umgehen Sie dieses Problem:</span><span class="sxs-lookup"><span data-stu-id="6fd21-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="6fd21-166">Erstellen Sie ein neues .NET Core-Klassenbibliotheksprojekt.</span><span class="sxs-lookup"><span data-stu-id="6fd21-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="6fd21-167">Fügen Sie im neuen Projekt Verweise hinzu, um das [Generieren von C#-Code aus *\*.proto*-Dateien](xref:grpc/basics#generated-c-assets) zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="6fd21-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="6fd21-168">Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="6fd21-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="6fd21-169">Fügen Sie der `<Protobuf>`-Elementgruppe *\*.proto*-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="6fd21-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="6fd21-170">Fügen Sie in der WPF-Anwendung einen Verweis auf das neue Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="6fd21-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="6fd21-171">Die WPF-Anwendung kann die in gRPC generierten Typen aus dem neuen Klassenbibliotheksprojekt verwenden.</span><span class="sxs-lookup"><span data-stu-id="6fd21-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
