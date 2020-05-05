---
title: Kurzlebige Datenschutzanbieter in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails der ASP.net Core kurzlebigen Datenschutzanbieter.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 22a332230e15256dc33fd1d06f2da3ea8d34d3bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776889"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="2fa02-103">Kurzlebige Datenschutzanbieter in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2fa02-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="2fa02-104">Es gibt Szenarien, in denen eine Anwendung eine drossöhe benötigt `IDataProtectionProvider`.</span><span class="sxs-lookup"><span data-stu-id="2fa02-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="2fa02-105">Der Entwickler kann z. b. einfach nur in einer Konsolenanwendung experimentieren, oder die Anwendung selbst ist flüchtig (es ist ein Skript oder ein Komponenten Testprojekt).</span><span class="sxs-lookup"><span data-stu-id="2fa02-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="2fa02-106">Um diese Szenarien zu unterstützen, enthält das Paket [Microsoft. aspnetcore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) einen-Typ `EphemeralDataProtectionProvider`.</span><span class="sxs-lookup"><span data-stu-id="2fa02-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="2fa02-107">Dieser Typ stellt eine grundlegende Implementierung `IDataProtectionProvider` von bereit, deren schlüsselrepository ausschließlich im Arbeitsspeicher gespeichert ist und nicht in einen Sicherungs Speicher geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="2fa02-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="2fa02-108">Jede Instanz von `EphemeralDataProtectionProvider` verwendet Ihren eigenen eindeutigen Hauptschlüssel.</span><span class="sxs-lookup"><span data-stu-id="2fa02-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="2fa02-109">`IDataProtector` Wenn eine, die sich auf einem `EphemeralDataProtectionProvider` befindet, eine geschützte Nutzlast generiert, kann diese Nutzlast daher nur von einem `IDataProtector` Äquivalent (bei der gleichen [Zweck](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) Kette) geschützt werden, `EphemeralDataProtectionProvider` der sich auf derselben Instanz befindet.</span><span class="sxs-lookup"><span data-stu-id="2fa02-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="2fa02-110">Das folgende Beispiel veranschaulicht das Instanziieren `EphemeralDataProtectionProvider` eines und dessen Verwendung zum schützen und Aufheben des Schutzes von Daten.</span><span class="sxs-lookup"><span data-stu-id="2fa02-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
