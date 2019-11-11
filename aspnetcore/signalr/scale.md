---
title: ASP.net Core SignalR Produktions Hosting und-Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie Leistungs-und Skalierungsprobleme in apps vermeiden, die ASP.net Core-SignalRverwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
no-loc:
- SignalR
ms.openlocfilehash: a215ce489746a7585cf4e72d4f04e0eac588b44c
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905726"
---
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a>ASP.net Core SignalR Hosting und Skalierung

Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)

In diesem Artikel werden die Überlegungen zu Hosting und Skalierung für apps mit hohem Datenverkehr erläutert, die ASP.net Core SignalRverwenden

## <a name="sticky-sessions"></a>Persistente Sitzungen

SignalR erfordert, dass alle HTTP-Anforderungen für eine bestimmte Verbindung vom gleichen Server Prozess verarbeitet werden. Wenn SignalR auf einer Serverfarm (mehrere Server) ausgeführt wird, müssen "persistente Sitzungen" verwendet werden. "Persistente Sitzungen" werden von einigen Lasten Ausgleichs Modulen auch als Sitzungs Affinität bezeichnet. Azure App Service mithilfe von [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) Anforderungen weiterleiten. Wenn Sie die Einstellung "arr-Affinität" in ihrer Azure App Service aktivieren, werden "persistente Sitzungen" aktiviert. Die einzigen Situationen, in denen keine persistenten Sitzungen erforderlich sind, sind:

1. Beim Hosting auf einem einzelnen Server in einem einzelnen Prozess.
1. Bei Verwendung des Azure-SignalR Dienstanbieter.
1. Wenn alle Clients so konfiguriert sind, dass Sie **nur** websockets verwenden, **und** die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) in der Client Konfiguration aktiviert ist.

In allen anderen Fällen (einschließlich der Verwendung der redis-Rückwand) muss die Serverumgebung für persistente Sitzungen konfiguriert werden.

Anleitungen zum Konfigurieren von Azure App Service für SignalRfinden Sie unter <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>TCP-Verbindungs Ressourcen

Die Anzahl der gleichzeitigen TCP-Verbindungen, die ein Webserver unterstützen kann, ist begrenzt. Standard-HTTP-Clients verwenden *kurzlebige* Verbindungen. Diese Verbindungen können geschlossen werden, wenn der Client in den Leerlauf wechselt und später erneut geöffnet wird. Auf der anderen Seite ist eine SignalR Verbindung *persistent*. SignalR Verbindungen bleiben offen, auch wenn der Client in den Leerlauf wechselt. In einer APP mit hohem Datenverkehr, die viele Clients bedient, können diese persistenten Verbindungen bewirken, dass Server die maximale Anzahl von Verbindungen erreichen.

Persistente Verbindungen verbrauchen auch zusätzlichen Arbeitsspeicher, um die einzelnen Verbindungen zu verfolgen.

Die intensive Verwendung von Verbindungs bezogenen Ressourcen durch SignalR kann sich auf andere Web-Apps auswirken, die auf demselben Server gehostet werden. Wenn SignalR geöffnet wird und die letzten verfügbaren TCP-Verbindungen enthält, stehen anderen Web-Apps auf dem gleichen Server auch keine Verbindungen mehr zur Verfügung.

Wenn auf einem Server keine Verbindungen mehr auftreten, werden zufällige Socketfehler und Fehler beim Zurücksetzen der Verbindung angezeigt. Beispiel:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Um zu verhindern, dass SignalR Ressourcenverwendung in anderen Web-Apps zu Fehlern führt, führen Sie SignalR auf anderen Servern aus als Ihre anderen Web-Apps.

Um zu verhindern, dass SignalR Ressourcenverwendung in einer SignalR-App Fehler verursacht, Skalieren Sie horizontal hoch, um die Anzahl der Verbindungen zu begrenzen, die ein Server verarbeiten muss.

## <a name="scale-out"></a>Skalieren

Eine APP, die SignalR verwendet, muss alle Verbindungen nachverfolgen, die für eine Serverfarm Probleme verursachen. Fügen Sie einen Server hinzu, der neue Verbindungen erhält, die von den anderen Servern nicht bekannt sind. Beispielsweise ist SignalR auf jedem Server im folgenden Diagramm nicht mit den Verbindungen auf den anderen Servern zu rechnen. Wenn SignalR auf einem der Server eine Nachricht an alle Clients senden möchte, wird die Nachricht nur an die Clients gesendet, die mit diesem Server verbunden sind.

![Skalierung [! Schel. No-Loc (signalr)] ohne Rückwand](scale/_static/scale-no-backplane.png)

Die Optionen für die Lösung dieses Problems sind der [Azure SignalR-Dienst](#azure-signalr-service) und die [redis-Rückwand](#redis-backplane).

## <a name="azure-opno-locsignalr-service"></a>Azure SignalR-Dienst

Beim Azure-SignalR Dienst handelt es sich um einen Proxy und nicht um eine Backplane. Jedes Mal, wenn ein Client eine Verbindung mit dem Server initiiert, wird der Client umgeleitet, um eine Verbindung mit dem Dienst herzustellen. Dieser Prozess wird in der folgenden Abbildung veranschaulicht:

![Herstellen einer Verbindung mit Azure [! Schel. No-Loc (signalr)]-Dienst](scale/_static/azure-signalr-service-one-connection.png)

Das Ergebnis ist, dass der Dienst alle Clientverbindungen verwaltet, während jeder Server nur eine kleine Konstante Anzahl von Verbindungen mit dem Dienst benötigt, wie im folgenden Diagramm dargestellt:

![Mit dem Dienst verbundene Clients, Server, die mit dem Dienst verbunden sind](scale/_static/azure-signalr-service-multiple-connections.png)

Diese Vorgehensweise zum horizontalen hochskalieren hat gegenüber der redis-Rückwand-Alternative mehrere Vorteile:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind nicht erforderlich, da Clients sofort an den Azure SignalR-Dienst umgeleitet werden, wenn Sie eine Verbindung herstellen.
* Eine SignalR-App kann basierend auf der Anzahl der gesendeten Nachrichten horizontal hochskaliert werden, während der Azure-SignalR Dienst für eine beliebige Anzahl von Verbindungen automatisch skaliert wird. Es könnten z. b. Tausende von Clients vorhanden sein. Wenn jedoch nur wenige Nachrichten pro Sekunde gesendet werden, muss die SignalR-APP nicht auf mehrere Server horizontal hochskaliert werden, um die Verbindungen selbst zu bewältigen.
* Eine SignalR-App verwendet nicht wesentlich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR.

Aus diesen Gründen wird der Azure SignalR-Dienst für alle ASP.net Core SignalR in Azure gehosteten apps empfohlen, einschließlich APP Service, VMS und Containern.

Weitere Informationen finden Sie in der [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis-Backplane

[Redis](https://redis.io/) ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher, der ein Messaging System mit einem Veröffentlichungs-/Abonnementmodell unterstützt. Die SignalR redis-Rückwand verwendet die Pub/Sub-Funktion, um Nachrichten an andere Server weiterzuleiten. Wenn ein Client eine Verbindung herstellt, werden die Verbindungsinformationen an die Backplane übermittelt. Wenn ein Server eine Nachricht an alle Clients senden möchte, sendet er an die Backplane. Die Rückwand kennt alle verbundenen Clients und die Server, auf denen Sie sich befinden. Die Nachricht wird über die entsprechenden Server an alle Clients gesendet. Dieser Vorgang wird in der folgenden Abbildung veranschaulicht:

![Redis-Backplane, von einem Server an alle Clients gesendete Nachrichten](scale/_static/redis-backplane.png)

Die redis-Rückwand ist der empfohlene Ansatz für horizontales Skalieren für apps, die in ihrer eigenen Infrastruktur gehostet werden. Azure SignalR Service ist aufgrund der Verbindungs Latenz zwischen Ihrem Rechenzentrum und einem Azure-Rechenzentrum nicht die praktische Option für den Einsatz in der Produktion mit lokalen apps.

Die zuvor notierten Vorteile von Azure SignalR Service sind Nachteile der redis-Backplane:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind erforderlich. Nachdem eine Verbindung auf einem Server initiiert wurde, muss die Verbindung auf diesem Server bestehen.
* Eine SignalR-app muss basierend auf der Anzahl von Clients horizontal hochskaliert werden, auch wenn nur wenige Nachrichten gesendet werden.
* Eine SignalR-App verwendet erheblich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-overview)
* [Einrichten einer redis-Rückwand](xref:signalr/redis-backplane)
