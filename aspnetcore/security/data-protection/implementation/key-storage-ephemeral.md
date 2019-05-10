---
title: Kurzlebige Datenschutzanbieter in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung von ASP.NET Core kurzlebige-Datenschutzanbieter.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895177"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Kurzlebige Datenschutzanbieter in ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Es gibt Szenarien, in denen eine Anwendung einen Weg werfen erfordert `IDataProtectionProvider`. Z. B. der Entwickler möglicherweise nur in einer Konsolenanwendung für die einmalige experimentiert werden, oder die Anwendung selbst ist flüchtig (es wird ein Skript erstellt oder ein Komponententestprojekt). Zur Unterstützung dieser Szenarios die [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) Paket enthält einen Typ `EphemeralDataProtectionProvider`. Dieser Typ stellt eine grundlegende Implementierung der `IDataProtectionProvider` , dessen Schlüssel Repository ist ausschließlich im Arbeitsspeicher gehalten und ist nicht in einen Sicherungsspeicher geschrieben.

Jede Instanz des `EphemeralDataProtectionProvider` verwendet einen eigenen eindeutigen Hauptschlüssel. Aus diesem Grund Wenn ein `IDataProtector` am Stamm einer `EphemeralDataProtectionProvider` generiert eine geschützte Nutzlast, diese Nutzlast kann nur durch ein entsprechendes ungeschützt sein `IDataProtector` (erhält die gleiche [Zweck](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) Kette) zur gleichen Stamm `EphemeralDataProtectionProvider` -Instanz.

Das folgende Beispiel veranschaulicht die Instanziierung einer `EphemeralDataProtectionProvider` und zum Schützen und Aufheben des Schutzes von Daten.

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
