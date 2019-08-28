---
title: Problembehandlung bei GrpC auf .net Core
author: jamesnk
description: Beheben von Fehlern bei der Verwendung von GrpC auf .net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/26/2019
uid: grpc/troubleshoot
ms.openlocfilehash: 49bde2792f0fd7910de02d75f5f443000916dec7
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70112753"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="841d9-103">Problembehandlung bei GrpC auf .net Core</span><span class="sxs-lookup"><span data-stu-id="841d9-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="841d9-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="841d9-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="841d9-105">In diesem Dokument werden häufig auftretende Probleme bei der Entwicklung von GrpC-apps in .net erläutert.</span><span class="sxs-lookup"><span data-stu-id="841d9-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="841d9-106">Keine Übereinstimmung zwischen Client-und Dienst-SSL/TLS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="841d9-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="841d9-107">Die GrpC-Vorlage und-Beispiele verwenden [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) , um GrpC-Dienste standardmäßig zu sichern.</span><span class="sxs-lookup"><span data-stu-id="841d9-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="841d9-108">GrpC-Clients müssen eine sichere Verbindung verwenden, um gesicherte GrpC-Dienste erfolgreich aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="841d9-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="841d9-109">Sie können überprüfen, ob der ASP.net Core GrpC-Dienst TLS in den in App-Start geschriebenen Protokollen verwendet.</span><span class="sxs-lookup"><span data-stu-id="841d9-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="841d9-110">Der Dienst lauscht an einem HTTPS-Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="841d9-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="841d9-111">Der .net Core-Client muss `https` in der Server Adresse verwenden, um Aufrufe mit einer gesicherten Verbindung durchführen zu können:</span><span class="sxs-lookup"><span data-stu-id="841d9-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="841d9-112">Alle GrpC-Client Implementierungen unterstützen TLS.</span><span class="sxs-lookup"><span data-stu-id="841d9-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="841d9-113">für GrpC-Clients aus anderen Sprachen ist in der Regel `SslCredentials`der mit konfigurierte Kanal erforderlich.</span><span class="sxs-lookup"><span data-stu-id="841d9-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="841d9-114">`SslCredentials`Gibt das Zertifikat an, das vom Client verwendet wird, und es muss anstelle unsicherer Anmelde Informationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="841d9-115">Beispiele für das Konfigurieren der verschiedenen GrpC-Client Implementierungen für die Verwendung von TLS finden Sie unter [GrpC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="841d9-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="841d9-116">Einen GrpC-Dienst mit einem nicht vertrauenswürdigen/ungültigen Zertifikat aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="841d9-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="841d9-117">Der .net-GrpC-Client erfordert, dass der Dienst über ein vertrauenswürdiges Zertifikat verfügt.</span><span class="sxs-lookup"><span data-stu-id="841d9-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="841d9-118">Die folgende Fehlermeldung wird zurückgegeben, wenn ein GrpC-Dienst ohne vertrauenswürdiges Zertifikat aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="841d9-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="841d9-119">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="841d9-119">Unhandled exception.</span></span> <span data-ttu-id="841d9-120">System .net. http. httprequestexception: Die SSL-Verbindung konnte nicht hergestellt werden. siehe innere Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="841d9-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="841d9-121">---> System. Security. Authentication. AuthenticationException: Das Remote Zertifikat ist gemäß dem Validierungsverfahren ungültig.</span><span class="sxs-lookup"><span data-stu-id="841d9-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="841d9-122">Dieser Fehler wird möglicherweise angezeigt, wenn Sie Ihre APP lokal testen und das ASP.net Core HTTPS-Entwicklungs Zertifikat nicht vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="841d9-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="841d9-123">Anweisungen zum Beheben dieses Problems finden Sie unter [Vertrauen des ASP.net Core HTTPS-Entwicklungs Zertifikats unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="841d9-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="841d9-124">Wenn Sie einen GrpC-Dienst auf einem anderen Computer aufrufen und dem Zertifikat nicht vertrauen können, kann der GrpC-Client so konfiguriert werden, dass das ungültige Zertifikat ignoriert wird.</span><span class="sxs-lookup"><span data-stu-id="841d9-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="841d9-125">Der folgende Code verwendet [httpclienthandler. servercertifitorecustomvalidationcallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) , um Aufrufe ohne ein vertrauenswürdiges Zertifikat zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="841d9-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpClientHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpClientHandler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) => true;

var httpClient = new HttpClient(httpClientHandler);
httpClient.BaseAddress = new Uri("https://localhost:5001");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="841d9-126">Nicht vertrauenswürdige Zertifikate sollten nur während der APP-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="841d9-127">In Produktions-apps sollten immer gültige Zertifikate verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="841d9-128">Anrufen unsicherer GrpC-Dienste mit .net Core-Client</span><span class="sxs-lookup"><span data-stu-id="841d9-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="841d9-129">Eine zusätzliche Konfiguration ist erforderlich, um unsichere GrpC-Dienste mit dem .net Core-Client aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="841d9-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="841d9-130">Der GrpC-Client muss den `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` Switch auf `true` festlegen und `http` in der Server Adresse verwenden:</span><span class="sxs-lookup"><span data-stu-id="841d9-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The address starts with "http://"
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="841d9-131">ASP.net Core GrpC-app unter macOS konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="841d9-132">Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht.</span><span class="sxs-lookup"><span data-stu-id="841d9-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="841d9-133">Die ASP.net Core GrpC-Vorlage und-Beispiele verwenden standardmäßig TLS.</span><span class="sxs-lookup"><span data-stu-id="841d9-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="841d9-134">Wenn Sie versuchen, den GrpC-Server zu starten, wird die folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="841d9-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="841d9-135">Bindung https://localhost:5001 an an der IPv4-Loopback Schnittstelle nicht möglich: HTTP/2 über TLS wird unter macOS aufgrund fehlender alpn-Unterstützung nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="841d9-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="841d9-136">Um dieses Problem zu umgehen, konfigurieren Sie Kestrel und den GrpC-Client für die Verwendung von http/2 *ohne* TLS.</span><span class="sxs-lookup"><span data-stu-id="841d9-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="841d9-137">Dies sollte nur während der Entwicklung durchzuführen sein.</span><span class="sxs-lookup"><span data-stu-id="841d9-137">You should only do this during development.</span></span> <span data-ttu-id="841d9-138">Die Verwendung von TLS führt dazu, dass GrpC-Nachrichten ohne Verschlüsselung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="841d9-139">Kestrel muss einen http/2-Endpunkt ohne TLS in *Program.cs*konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="841d9-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

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

<span data-ttu-id="841d9-140">Wenn ein http/2-Endpunkt ohne TLS konfiguriert ist, müssen die [listenoptions.-Protokolle](xref:fundamentals/servers/kestrel#listenoptionsprotocols) des Endpunkts `HttpProtocols.Http2`auf festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="841d9-141">`HttpProtocols.Http1AndHttp2`kann nicht verwendet werden, da TLS zum Aushandeln von http/2 erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="841d9-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="841d9-142">Ohne TLS werden alle Verbindungen mit dem Endpunkt standardmäßig auf HTTP/1.1, und GrpC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="841d9-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="841d9-143">Der GrpC-Client muss auch für die Verwendung von TLS konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="841d9-144">Weitere Informationen finden Sie unter [callunsichere GrpC-Dienste mit .net Core-Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="841d9-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="841d9-145">HTTP/2 ohne TLS sollte nur während der APP-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="841d9-146">Produktions-apps sollten immer Transportsicherheit verwenden.</span><span class="sxs-lookup"><span data-stu-id="841d9-146">Production apps should always use transport security.</span></span> <span data-ttu-id="841d9-147">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in GrpC für ASP.net Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="841d9-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="841d9-148">GrpC C# -Assets werden nicht aus  *\*. proto* -Dateien generiert.</span><span class="sxs-lookup"><span data-stu-id="841d9-148">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="841d9-149">die GrpC-Codegenerierung von konkreten Clients und Dienst Basisklassen erfordert, dass protobuf-Dateien und-Tools von einem Projekt referenziert werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="841d9-150">Sie müssen Folgendes einschließen:</span><span class="sxs-lookup"><span data-stu-id="841d9-150">You must include:</span></span>

* <span data-ttu-id="841d9-151">*. proto* -Dateien, die Sie in der `<Protobuf>` Element Gruppe verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="841d9-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="841d9-152">Auf [importierte *. proto* -Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss vom Projekt verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="841d9-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="841d9-153">Paket Verweis auf das GrpC-Toolpaket " [GrpC. Tools](https://www.nuget.org/packages/Grpc.Tools/)".</span><span class="sxs-lookup"><span data-stu-id="841d9-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="841d9-154">Weitere Informationen zum Erstellen von GrpC C# -Assets finden <xref:grpc/basics>Sie unter.</span><span class="sxs-lookup"><span data-stu-id="841d9-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="841d9-155">Standardmäßig generiert ein `<Protobuf>` Verweis einen konkreten Client und eine Dienst Basisklasse.</span><span class="sxs-lookup"><span data-stu-id="841d9-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="841d9-156">Das-Attribut des `GrpcServices` Reference-Elements kann verwendet werden C# , um die Medienobjekt Generierung einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="841d9-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="841d9-157">Gültige `GrpcServices` Optionen sind:</span><span class="sxs-lookup"><span data-stu-id="841d9-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="841d9-158">`Both`(Standardwert, wenn nicht vorhanden)</span><span class="sxs-lookup"><span data-stu-id="841d9-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="841d9-159">Eine ASP.net Core Web-App, die GrpC-Dienste gehostet, benötigt nur die generierte Dienst Basisklasse:</span><span class="sxs-lookup"><span data-stu-id="841d9-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="841d9-160">Für eine GrpC-Client-App, die GrpC-Anrufe erstellt, muss nur der konkrete Client generiert werden</span><span class="sxs-lookup"><span data-stu-id="841d9-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
