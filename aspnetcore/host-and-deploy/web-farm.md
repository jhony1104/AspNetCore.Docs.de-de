---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/web-farm
ms.openlocfilehash: 37a50fa2939eb31e66925fa907212d54577bd20b
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106571"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>'Identity'

'Let's Encrypt'

'Razor' 'SignalR' uid: Hosten von ASP.NET Core in einer Webfarm

* Von [Chris Ross](https://github.com/Tratcher)
* Eine *Webfarm* ist eine Gruppe von mindestens zwei Webservern (oder *Knoten*), die mehrere Instanzen einer App hostet. Wenn eine Webfarm Benutzeranforderungen empfängt, verteilt ein *Lastenausgleichsmodul* die Anforderungen auf die Knoten der Webfarm.
* Webfarmen verbessern: **Zuverlässigkeit/Verfügbarkeit:** Wenn bei mindestens einem Knoten ein Fehler auftritt, kann der Lastenausgleich Anforderungen zur weiteren Verarbeitung an andere funktionierende Knoten weiterleiten.
* **Kapazität/Leistung:** Mehrere Knoten können mehr Anforderungen verarbeiten als ein einzelner Server. Das Lastenausgleichsmodul verteilt den Workload, indem Anforderungen auf die Knoten verteilt werden.

**Skalierbarkeit:** Wenn mehr oder weniger Kapazität benötigt wird, kann die Anzahl der aktiven Knoten entsprechend der Arbeitsauslastung erhöht oder verringert werden.

## <a name="general-configuration"></a>Webfarmtechnologien wie [Azure App Service](https://azure.microsoft.com/services/app-service/) können Knoten automatisch auf Systemadministratoranforderungen hin hinzufügen oder entfernen bzw. diese Vorgänge komplett ohne manuelles Eingreifen ausführen.

<xref:host-and-deploy/index>  
**Wartbarkeit**: Knoten einer Webfarm können auf einer Reihe von gemeinsamen Diensten basieren, wodurch die Systemverwaltung vereinfacht wird. Beispielsweise können die Knoten einer Webfarm auf einem einzigen Datenbankserver und einer gemeinsamen Netzwerkadresse für statische Ressourcen wie Images und herunterladbare Dateien basieren. Dieses Thema beschreibt die Konfiguration und Abhängigkeiten für ASP.NET Core-Apps, die in einer Webfarm gehostet werden und auf freigegebenen Ressourcen basieren. Allgemeine Konfiguration

<xref:host-and-deploy/proxy-load-balancer>  
Erfahren Sie, wie Sie Hostingumgebungen einrichten und ASP.NET Core-Apps bereitstellen.

<xref:host-and-deploy/azure-apps/index>  
Konfigurieren Sie einen Prozess-Manager auf jedem Knoten der Webfarm, um App-Starts und -Neustarts zu automatisieren. Jeder Knoten benötigt die ASP.NET Core-Laufzeit.

## <a name="app-data"></a>Weitere Informationen finden Sie in der Dokumentation [Hosten und Bereitstellen von ASP.NET Core](xref:host-and-deploy/index).

Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden [Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core. App Service ist eine vollständig verwaltete Plattform, die automatisches Skalieren, Lastenausgleich, Patchen und Continuous Deployment bietet.

## <a name="required-configuration"></a>App-Daten

Wenn eine App auf mehrere Instanzen skaliert wird, muss der App-Status möglicherweise über Knoten hinweg freigegeben werden.

### <a name="data-protection"></a>Im Falle eines Übergangsstatus sollten Sie [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) freigeben.

Wenn der freigegebene Status Persistenz erfordert, speichern Sie ihn in einer Datenbank. Erforderliche Konfiguration Schutz von Daten und Zwischenspeichern benötigen eine in einer Webfarm bereitgestellte App-Konfiguration. Schutz von Daten Der [ASP.NET Core-Datenschutz](xref:security/data-protection/introduction) wird von Apps verwendet, um Daten zu schützen. Der Schutz von Daten basiert auf einem Satz kryptografischer Schlüssel in einer *Schlüsselsammlung*. Wenn das System zum Schutz von Daten initialisiert wird, werden [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) angewendet, die die Schlüsselsammlung lokal speichern. In der Standardkonfiguration wird eine eindeutige Schlüsselsammlung auf jedem Knoten der Webfarm gespeichert.

### <a name="caching"></a>Folglich kann kein Webfarmknoten Daten entschlüsseln, die von einer App auf einem anderen Knoten verschlüsselt werden.

Die Standardkonfiguration eignet sich in der Regel nicht für das Hosten von Apps in einer Webfarm. Eine Alternative zur Implementierung einer freigegebenen Schlüsselsammlung besteht darin, Benutzeranforderungen immer an denselben Knoten weiterzuleiten. Weitere Informationen zur Systemkonfiguration zum Schutz von Daten für Webfarmbereitstellungen finden Sie unter <xref:security/data-protection/configuration/overview>.

## <a name="dependent-components"></a>Zwischenspeicherung

In einer Webfarmumgebung muss der Zwischenspeichermechanismus zwischengespeicherte Elemente über die Webfarmknoten hinweg freigeben.

| Das Zwischenspeichern muss auf einem Redis Cache, einer freigegebenen SQL Server-Datenbank oder einer benutzerdefinierten Zwischenspeicherimplementierung basieren, die zwischengespeicherte Elemente über die Webfarm hinweg freigibt. | Weitere Informationen finden Sie unter <xref:performance/caching/distributed>. |
| -------- | ------------------- |
| Abhängige Komponenten | Die folgenden Szenarien erfordern keine zusätzliche Konfiguration, hängen jedoch von Technologien ab, die eine Webfarmkonfiguration benötigen.<br><br>Szenario |
| Identity | Abhängig von&hellip;<br><br>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| 'Blazor' | 'Identity'<br><br>'Let's Encrypt' |
| 'Razor' | 'SignalR' uid:<br><br>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| 'Blazor' | 'Identity'<br><br>'Let's Encrypt' |

## <a name="troubleshoot"></a>'Razor'

### <a name="data-protection-and-caching"></a>'SignalR' uid:

---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

* 'Blazor' 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity' 'Let's Encrypt'

## <a name="obtain-data-from-apps"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

## <a name="additional-resources"></a>'Blazor'

* 'Identity'
* <xref:host-and-deploy/proxy-load-balancer>
 