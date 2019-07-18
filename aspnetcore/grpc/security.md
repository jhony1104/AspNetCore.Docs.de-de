---
title: Sicherheitsüberlegungen in GrpC für ASP.net Core
author: jamesnk
description: Erfahren Sie mehr über die Sicherheitsüberlegungen für GrpC für ASP.net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: 4a70cb16d8397dbc69a626435fb72a0512788b14
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308753"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="487fb-103">Sicherheitsüberlegungen in GrpC für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="487fb-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="487fb-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="487fb-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="487fb-105">Dieser Artikel enthält Informationen zum Sichern von GrpC mit .net Core.</span><span class="sxs-lookup"><span data-stu-id="487fb-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="487fb-106">Transport Sicherheit</span><span class="sxs-lookup"><span data-stu-id="487fb-106">Transport security</span></span>

<span data-ttu-id="487fb-107">GrpC-Nachrichten werden mithilfe von http/2 gesendet und empfangen.</span><span class="sxs-lookup"><span data-stu-id="487fb-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="487fb-108">Wir empfehlen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="487fb-108">We recommend:</span></span>

* <span data-ttu-id="487fb-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) zum Sichern von Nachrichten in Produktions-GrpC-Apps verwendet.</span><span class="sxs-lookup"><span data-stu-id="487fb-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="487fb-110">GrpC-Dienste sollten nur gesicherte Ports lauschen und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="487fb-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="487fb-111">TLS ist in Kestrel konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="487fb-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="487fb-112">Weitere Informationen zum Konfigurieren von Kestrel-Endpunkten finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="487fb-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="487fb-113">Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="487fb-113">Exceptions</span></span>

<span data-ttu-id="487fb-114">Ausnahme Meldungen werden im Allgemeinen als sensible Daten betrachtet, die für einen Client nicht offengelegt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="487fb-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="487fb-115">Standardmäßig sendet GrpC nicht die Details einer Ausnahme, die von einem GrpC-Dienst ausgelöst wird, an den Client.</span><span class="sxs-lookup"><span data-stu-id="487fb-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="487fb-116">Stattdessen empfängt der Client eine generische Meldung, die angibt, dass ein Fehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="487fb-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="487fb-117">Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit " [enabledetailederrors](xref:grpc/configuration#configure-services-options)".</span><span class="sxs-lookup"><span data-stu-id="487fb-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="487fb-118">Ausnahme Meldungen sollten nicht in Produktions-Apps für den Client verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="487fb-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="487fb-119">Größen Limits für Nachrichten</span><span class="sxs-lookup"><span data-stu-id="487fb-119">Message size limits</span></span>

<span data-ttu-id="487fb-120">Eingehende Nachrichten an GrpC-Clients und-Dienste werden in den Arbeitsspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="487fb-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="487fb-121">Die Größenbeschränkungen für Nachrichten sind ein Mechanismus, mit dem verhindert wird, dass GrpC übermäßig viele Ressourcen beansprucht.</span><span class="sxs-lookup"><span data-stu-id="487fb-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="487fb-122">GrpC verwendet Größenbeschränkungen pro Nachricht, um eingehende und ausgehende Nachrichten zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="487fb-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="487fb-123">Standardmäßig schränkt der GrpC eingehende Nachrichten auf 4 MB ein.</span><span class="sxs-lookup"><span data-stu-id="487fb-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="487fb-124">Es gibt keine Beschränkung für ausgehende Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="487fb-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="487fb-125">Auf dem-Server können die GrpC-Nachrichten Limits für alle Dienste in einer APP mit `AddGrpc`folgenden Aktionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="487fb-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 1 * 1024 * 1024;  // 1 megabyte
        options.SendMaxMessageSize = 1 * 1024 * 1024;     // 1 megabyte
    });
}
```

<span data-ttu-id="487fb-126">Grenzwerte können auch für einen einzelnen Dienst mithilfe `AddServiceOptions<TService>`von konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="487fb-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="487fb-127">Weitere Informationen zum Konfigurieren von Größen Limits für Nachrichten finden Sie unter [GrpC-Konfiguration](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="487fb-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="487fb-128">Überprüfung des Client Zertifikats</span><span class="sxs-lookup"><span data-stu-id="487fb-128">Client certificate validation</span></span>

<span data-ttu-id="487fb-129">[Client Zertifikate](https://tools.ietf.org/html/rfc5246#section-7.4.4) werden anfänglich überprüft, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="487fb-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="487fb-130">Standardmäßig führt Kestrel keine zusätzliche Überprüfung des Client Zertifikats einer Verbindung durch.</span><span class="sxs-lookup"><span data-stu-id="487fb-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="487fb-131">Es wird empfohlen, dass die von Client Zertifikaten gesicherten GrpC-Dienste das [Microsoft. aspnetcore. Authentication. Certificate](xref:security/authentication/certauth) -Paket verwenden.</span><span class="sxs-lookup"><span data-stu-id="487fb-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="487fb-132">ASP.net Core Zertifizierungs Authentifizierung führt eine zusätzliche Überprüfung für ein Client Zertifikat durch, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="487fb-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="487fb-133">Das Zertifikat hat eine gültige erweiterte Schlüssel Verwendung (EKU).</span><span class="sxs-lookup"><span data-stu-id="487fb-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="487fb-134">Liegt innerhalb seines Gültigkeits Zeitraums</span><span class="sxs-lookup"><span data-stu-id="487fb-134">Is within its validity period</span></span>
* <span data-ttu-id="487fb-135">Zertifikat Sperrung überprüfen</span><span class="sxs-lookup"><span data-stu-id="487fb-135">Check certificate revocation</span></span>
