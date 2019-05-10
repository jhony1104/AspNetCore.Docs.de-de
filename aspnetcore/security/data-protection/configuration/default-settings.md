---
title: Data Protection-schlüsselverwaltung und Lebensdauer in ASP.NET Core
author: rick-anderson
description: Informationen Sie zur schlüsselverwaltung für den Schutz von Daten und die Lebensdauer in ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 2f022a4c7519485fe629ce47c27d214c8c27d5bc
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897277"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Data Protection-schlüsselverwaltung und Lebensdauer in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Schlüsselverwaltung

Die app versucht, seiner betriebsumgebung erkennen und Konfiguration selbst zu behandeln.

1. Wenn die app, in gehostet wird [Azure Apps](https://azure.microsoft.com/services/app-service/), Schlüssel werden beibehalten, um die *%HOME%\ASP.NET\DataProtection-Keys* Ordner. Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert.
   * Ruhende Schlüssel werden nicht geschützt.
   * Die *DataProtection-Schlüssel* Ordner stellt den Schlüsselbund für alle Instanzen einer App in einem einzelnen bereitstellungsslot bereit.
   * Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund. Wenn Sie zwischen bereitstellungsslots, z. B. Staging-und Produktionsumgebung austauschen oder mithilfe von austauschen / B-Tests, keine app, die den Schutz von Daten mit gespeicherte Daten, die mit dem Schlüsselbund des vorherigen Slots entschlüsseln. Dies führt zu Benutzern, die aus einer Anwendung, die die standardmäßige ASP.NET Core-Cookie-Authentifizierung verwendet protokolliert wird, da sie den Schutz von Daten zum Schutz ihrer Cookies verwendet. Wenn Sie unabhängige Slot Schlüssel Ringe wünschen, verwenden Sie einen externen Schlüsselbund-Anbieter, z. B. Azure Blob Storage, Azure Key Vault, eine SQL-Speicher oder Redis-Cache.

1. Wenn das Benutzerprofil verfügbar ist, werden Schlüssel zum Beibehalten der *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* Ordner. Wenn das Betriebssystem Windows ist, werden die Schlüssel im Ruhezustand mit DPAPI verschlüsselt.

   Das [setProfileEnvironment-Attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) des App-Pools muss ebenfalls aktiviert sein. Der Standardwert von `setProfileEnvironment` ist `true`. In einigen Szenarien (z.B. Windows-Betriebssystem) ist `setProfileEnvironment` auf `false` festgelegt. Gehen Sie folgendermaßen vor, wenn Schlüssel nicht wie erwartet im Benutzerprofilverzeichnis gespeichert werden:

   1. Navigieren Sie zum Ordner *%windir%/system32/inetsrv/config*.
   1. Öffnen Sie die Datei *applicationHost.config*.
   1. Suchen Sie das Element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
   1. Bestätigen Sie, dass das `setProfileEnvironment`-Attribut nicht vorhanden ist, das standardmäßig den Wert `true` aufweist, oder legen Sie den Wert des Attributs explizit auf `true` fest.

1. Wenn die app in IIS gehostet wird, werden die Schlüssel in einen speziellen Registrierungsschlüssel der HKLM-Registrierung beibehalten, die nur in das Workerprozesskonto ACL des workerprozesskontos bereitgestellt wird. Schlüssel werden im Ruhezustand mit DPAPI verschlüsselt.

1. Wenn keine dieser Bedingungen übereinstimmen, werden nicht die Schlüssel außerhalb des aktuellen Prozesses beibehalten. Wenn der Prozess alle generierten heruntergefahren wird, sind die Schlüssel verloren gehen.

Der Entwickler hat immer vollständige Kontrolle und kann außer Kraft setzen, wie und wo der Schlüssel gespeichert sind. Die ersten drei oben aufgeführten Optionen sollten gute Standardwerte bereitstellen, für die meisten apps ähnlich wie ASP.NET  **\<MachineKey >** automatische Generierung von Routinen in der Vergangenheit war. Die endgültige, alternative Option ist das einzige Szenario, die den Entwickler angeben erfordert [Konfiguration](xref:security/data-protection/configuration/overview) im Vorfeld, wenn sie schlüsselpersistenz, aber diese Vorgehensweise ist nur in seltenen Fällen auftritt.

Wenn Sie in einem Docker-Container zu hosten, Schlüssel beibehalten werden soll in einem Ordner, der ein Docker-Volume (ein freigegebenes Volume oder einem Host eingebundenen Volume, die außerhalb des Containers Lebensdauer beibehalten) oder in einem externen Anbieter, wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [Redis](https://redis.io/). Ein externer Anbieter ist auch in der Webfarm-Szenarien nützlich, wenn apps nicht auf ein Volume des freigegebenen Netzwerkpfad zugreifen können (siehe [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) Informationen).

> [!WARNING]
> Wenn der Entwickler die oben beschriebenen Regeln überschrieben und das System den Schutz von Daten an ein bestimmtes Schlüssel-Repository zeigt, ist die automatische Verschlüsselung von Schlüsseln im ruhenden Zustand deaktiviert. Im Ruhezustand Protection wieder aktiviert werden kann [Konfiguration](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Die Gültigkeitsdauer

Schlüssel nutzen, eine 90-Tage-Lebensdauer. Wenn ein Schlüssel abläuft, wird die app automatisch generiert einen neuen Schlüssel, und legt den neuen Schlüssel als aktiver Schlüssel. Als veraltet Schlüssel auf dem System bleiben, kann Ihre app alle mit der sie geschützten Daten entschlüsseln. Finden Sie unter [schlüsselverwaltung](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) für Weitere Informationen.

## <a name="default-algorithms"></a>Standardalgorithmen

Die Nutzlast Schutz verwendete Standardalgorithmus ist AES-256-CBC für Vertraulichkeit und HMACSHA256 Authentizität. 512-Bit-Hauptschlüssel, alle 90 Tage geändert wird verwendet, die zwei untergeordneten Schlüssel verwendet, die für diese Algorithmen auf Basis pro Nutzlast abgeleitet werden. Finden Sie unter [Unterschlüssel Ableitung](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) für Weitere Informationen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
