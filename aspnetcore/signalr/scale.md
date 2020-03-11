---
title: ASP.net Core SignalR Produktions Hosting und-Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie Leistungs-und Skalierungsprobleme in apps vermeiden, die ASP.net Core-SignalRverwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: 260e2f0c16288fec2e0a694d070f357529782d8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655159"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.net Core signalr-Hosting und-Skalierung

Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)

In diesem Artikel werden die Überlegungen zu Hosting und Skalierung für apps mit hohem Datenverkehr erläutert, die ASP.net Core signalr verwenden

## <a name="sticky-sessions"></a>Persistente Sitzungen

Signalr erfordert, dass alle HTTP-Anforderungen für eine bestimmte Verbindung vom gleichen Server Prozess verarbeitet werden. Wenn signalr in einer Serverfarm (mehrere Server) ausgeführt wird, müssen "persistente Sitzungen" verwendet werden. "Persistente Sitzungen" werden von einigen Lasten Ausgleichs Modulen auch als Sitzungs Affinität bezeichnet. Azure App Service mithilfe von [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) Anforderungen weiterleiten. Wenn Sie die Einstellung "arr-Affinität" in ihrer Azure App Service aktivieren, werden "persistente Sitzungen" aktiviert. Die einzigen Situationen, in denen keine persistenten Sitzungen erforderlich sind, sind:

1. Beim Hosting auf einem einzelnen Server in einem einzelnen Prozess.
1. Wenn Sie den Azure signalr-Dienst verwenden.
1. Wenn alle Clients so konfiguriert sind, dass Sie **nur** websockets verwenden, **und** die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) in der Client Konfiguration aktiviert ist.

In allen anderen Fällen (einschließlich der Verwendung der redis-Rückwand) muss die Serverumgebung für persistente Sitzungen konfiguriert werden.

Anleitungen zum Konfigurieren von Azure App Service für signalr finden Sie unter <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>TCP-Verbindungs Ressourcen

Die Anzahl der gleichzeitigen TCP-Verbindungen, die ein Webserver unterstützen kann, ist begrenzt. Standard-HTTP-Clients verwenden *kurzlebige* Verbindungen. Diese Verbindungen können geschlossen werden, wenn der Client in den Leerlauf wechselt und später erneut geöffnet wird. Auf der anderen Seite ist eine signalr-Verbindung *persistent*. Signalr-Verbindungen bleiben offen, auch wenn der Client in den Leerlauf wechselt. In einer APP mit hohem Datenverkehr, die viele Clients bedient, können diese persistenten Verbindungen bewirken, dass Server die maximale Anzahl von Verbindungen erreichen.

Persistente Verbindungen verbrauchen auch zusätzlichen Arbeitsspeicher, um die einzelnen Verbindungen zu verfolgen.

Die intensive Verwendung von Verbindungs bezogenen Ressourcen durch signalr kann sich auf andere Web-Apps auswirken, die auf demselben Server gehostet werden. Wenn signalr die letzten verfügbaren TCP-Verbindungen öffnet und enthält, sind auch andere Web-Apps auf demselben Server nicht mehr verfügbar.

Wenn auf einem Server keine Verbindungen mehr auftreten, werden zufällige Socketfehler und Fehler beim Zurücksetzen der Verbindung angezeigt. Beispiel:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Um die signalr-Ressourcenverwendung von Fehlern in anderen Web-Apps zu verhindern, führen Sie signalr auf anderen Servern als Ihre anderen Web-Apps aus.

Um die signalr-Ressourcenverwendung von Fehlern in einer signalr-APP zu verhindern, Skalieren Sie horizontal hoch, um die Anzahl der Verbindungen zu begrenzen, die ein Server verarbeiten muss.

## <a name="scale-out"></a>Horizontales Skalieren

Eine APP, die signalr verwendet, muss alle Verbindungen nachverfolgen, die für eine Serverfarm Probleme verursachen. Fügen Sie einen Server hinzu, der neue Verbindungen erhält, die von den anderen Servern nicht bekannt sind. Beispielsweise sind in signalr auf jedem Server im folgenden Diagramm die Verbindungen auf den anderen Servern nicht bekannt. Wenn signalr auf einem der Server eine Nachricht an alle Clients senden möchte, wird die Nachricht nur an die Clients gesendet, die mit diesem Server verbunden sind.

![Skalieren von signalr ohne Rückwand](scale/_static/scale-no-backplane.png)

Die Optionen für die Lösung dieses Problems sind der [Azure signalr-Dienst](#azure-signalr-service) und die [redis-Rückwand](#redis-backplane).

## <a name="azure-signalr-service"></a>Azure SignalR Service

Der Azure signalr-Dienst ist ein Proxy anstelle einer Backplane. Jedes Mal, wenn ein Client eine Verbindung mit dem Server initiiert, wird der Client umgeleitet, um eine Verbindung mit dem Dienst herzustellen. Dieser Prozess wird in der folgenden Abbildung veranschaulicht:

![Herstellen einer Verbindung mit dem Azure signalr-Dienst](scale/_static/azure-signalr-service-one-connection.png)

Das Ergebnis ist, dass der Dienst alle Clientverbindungen verwaltet, während jeder Server nur eine kleine Konstante Anzahl von Verbindungen mit dem Dienst benötigt, wie im folgenden Diagramm dargestellt:

![Mit dem Dienst verbundene Clients, Server, die mit dem Dienst verbunden sind](scale/_static/azure-signalr-service-multiple-connections.png)

Diese Vorgehensweise zum horizontalen hochskalieren hat gegenüber der redis-Rückwand-Alternative mehrere Vorteile:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind nicht erforderlich, da Clients sofort an den Azure signalr-Dienst umgeleitet werden, wenn Sie eine Verbindung herstellen.
* Eine signalr-App kann basierend auf der Anzahl der gesendeten Nachrichten horizontal hochskaliert werden, während der Azure signalr-Dienst für eine beliebige Anzahl von Verbindungen automatisch skaliert wird. Es könnten z. b. Tausende von Clients vorhanden sein. Wenn jedoch nur wenige Nachrichten pro Sekunde gesendet werden, muss die signalr-APP nicht auf mehrere Server horizontal hochskaliert werden, um die Verbindungen selbst zu bewältigen.
* Eine signalr-App verwendet nicht wesentlich mehr Verbindungs Ressourcen als eine Web-App ohne signalr.

Aus diesen Gründen wird der Azure signalr-Dienst für alle ASP.net Core signalr-apps empfohlen, die in Azure gehostet werden, einschließlich APP Service, VMS und Containern.

Weitere Informationen finden Sie in der [Dokumentation zu Azure signalr Service](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis-Backplane

[Redis](https://redis.io/) ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher, der ein Messaging System mit einem Veröffentlichungs-/Abonnementmodell unterstützt. Die signalr redis-Rückwand verwendet die Pub/Sub-Funktion, um Nachrichten an andere Server weiterzuleiten. Wenn ein Client eine Verbindung herstellt, werden die Verbindungsinformationen an die Backplane übermittelt. Wenn ein Server eine Nachricht an alle Clients senden möchte, sendet er an die Backplane. Die Rückwand kennt alle verbundenen Clients und die Server, auf denen Sie sich befinden. Die Nachricht wird über die entsprechenden Server an alle Clients gesendet. Dieser Vorgang wird in der folgenden Abbildung veranschaulicht:

![Redis-Backplane, von einem Server an alle Clients gesendete Nachrichten](scale/_static/redis-backplane.png)

Die redis-Rückwand ist der empfohlene Ansatz für horizontales Skalieren für apps, die in ihrer eigenen Infrastruktur gehostet werden. Wenn zwischen Ihrem Rechenzentrum und einem Azure-Rechenzentrum eine beträchtliche Verbindungs Wartezeit vorliegt, ist der Azure signalr-Dienst möglicherweise nicht die praktische Option für lokale apps mit geringer Latenz oder hohen Durchsatzanforderungen.

Die zuvor notierten Vorteile des Azure signalr Service sind Nachteile der redis-Backplane:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind erforderlich, außer wenn **beide** der folgenden Punkte zutreffen:
  * Alle Clients sind so konfiguriert, dass Sie **nur** websockets verwenden.
  * Die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) ist in der Client Konfiguration aktiviert. 
   Nachdem eine Verbindung auf einem Server initiiert wurde, muss die Verbindung auf diesem Server bestehen.
* Eine SignalR-app muss basierend auf der Anzahl von Clients horizontal hochskaliert werden, auch wenn nur wenige Nachrichten gesendet werden.
* Eine SignalR-App verwendet erheblich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR.

## <a name="iis-limitations-on-windows-client-os"></a>IIS-Einschränkungen für Windows-Client Betriebssystem

Windows 10 und Windows 8. x sind Client Betriebssysteme. IIS unter Client Betriebssystemen hat eine Beschränkung von 10 gleichzeitigen Verbindungen. SignalRVerbindungen sind:

* Vorübergehend und häufig wieder hergestellt.
* Wird **nicht** sofort verworfen, wenn Sie nicht mehr verwendet wird.

Die vorangehenden Bedingungen machen es wahrscheinlich, dass die 10 Verbindungs Beschränkung für ein Client Betriebssystem erreicht wird. Wenn ein Client Betriebssystem für die Entwicklung verwendet wird, wird Folgendes empfohlen:

* Vermeiden Sie IIS.
* Verwenden Sie Kestrel oder IIS Express als Bereitstellungs Ziele.

## <a name="linux-with-nginx"></a>Linux mit Nginx

Legen Sie die `Connection`-und `Upgrade` Header des Proxys für SignalR websockets auf Folgendes fest:

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Weitere Informationen finden Sie unter [NGINX as a WebSocket Proxy (NGINX als Websocket-Proxy)](https://www.nginx.com/blog/websocket-nginx/).

## <a name="third-party-opno-locsignalr-backplane-providers"></a>Drittanbieter SignalR Rückwand-Anbieter

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Tro](https://github.com/OrleansContrib/SignalR.Orleans)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-overview)
* [Einrichten einer redis-Rückwand](xref:signalr/redis-backplane)
