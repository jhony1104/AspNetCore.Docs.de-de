---
title: Hosten von ASP.NET Core SignalR Produktion und Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie vermeiden, Leistungs- und Skalierungsproblemen in apps, die ASP.NET Core SignalR verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 4ac4509acc89d0091a3757c7cfbc9981614f29ad
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895077"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hosten von ASP.NET Core SignalR und Skalierung

Durch [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), und [Tom Dykstra](https://github.com/tdykstra),

In diesem Artikel wird erläutert, hosten und Skalieren bei hohem Datenverkehrsaufkommen-apps, die ASP.NET Core SignalR verwenden.

## <a name="tcp-connection-resources"></a>TCP-Verbindungsressourcen

Die Anzahl der gleichzeitigen TCP-Verbindungen, die ein Webserver unterstützen kann, ist beschränkt. Standard-HTTP-Clients verwenden *kurzlebige* Verbindungen. Diese Verbindungen können geschlossen werden, wenn der Client in den Leerlauf übergeht und erneut geöffnet später noch Mal wird. Auf der anderen Seite eine SignalR-Verbindung ist *persistente*. SignalR-Verbindungen bleiben geöffnet, auch wenn der Client in den Leerlauf. In einer app mit hohem Datenverkehr, die Anzahl der Clients dient, kann diese persistenten Verbindungen Server die maximale Anzahl von Verbindungen erreicht.

Permanente Verbindungen nutzen auch einige zusätzlichen Arbeitsspeicher, um jede Verbindung zu verfolgen.

Die genutzten Ressourcen im Zusammenhang mit Verbindung von SignalR kann andere Web-apps beeinträchtigen, die auf demselben Server gehostet werden. Wenn SignalR wird geöffnet und die letzten verfügbaren TCP-Verbindungen enthält, haben andere Web-apps auf dem gleichen Server auch keine weiteren Verbindungen, die ihnen zur Verfügung.

Wenn ein Mangel an Verbindungen ausgeführt wird, werden zufällige Socketfehler angezeigt, und Verbindung zurückzusetzen, Fehler. Zum Beispiel:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Führen Sie zum verhindern, dass SignalR-ressourcenauslastung verursachen Fehler in anderen Web-apps, SignalR auf anderen Servern als Ihre Web-apps.

Zum verhindern, dass SignalR-ressourcenauslastung verursachen Fehler in einer SignalR-app, horizontal hochskalieren, um die Anzahl der Verbindungen zu beschränken, die ein Server behandelt.

## <a name="scale-out"></a>Skalieren

Eine app, die SignalR verwendet muss zum Nachverfolgen der Verbindungen, die Probleme, die für eine Serverfarm erstellt. Hinzufügen eines Servers ein, und wird auf neue Verbindungen, denen nicht die anderen Server zu kennen. SignalR auf jedem Server in der folgenden Abbildung ist beispielsweise nicht über die Verbindungen auf den anderen Servern. Wenn möchte, dass SignalR für einen der Server eine Nachricht an alle Clients zu senden, wird die Nachricht nur an die Clients, die mit dem Server verbunden sind.

![Skalierung von SignalR ohne Rückwandplatine](scale/_static/scale-no-backplane.png)

Die Optionen zur Lösung dieses Problems sind die [Azure SignalR Service](#azure-signalr-service) und [Redis Rückwandplatine](#redis-backplane).

## <a name="azure-signalr-service"></a>Azure SignalR-Dienst

Der Azure SignalR Service ist eine Rückwandplatine, anstatt einen Proxy. Jedes Mal, die ein Client eine Verbindung mit dem Server initiiert wird der Client umgeleitet, um eine Verbindung mit dem Dienst herzustellen. Dieser Prozess wird im folgenden Diagramm dargestellt:

![Herstellen einer Verbindung mit der Azure SignalR Service](scale/_static/azure-signalr-service-one-connection.png)

Das Ergebnis ist, dass der Dienst verwaltet alle Clientverbindungen, die während jeder Server nur eine kleine Konstante Anzahl von Verbindungen mit dem Dienst benötigt, wie im folgenden Diagramm dargestellt:

![Clients, die mit dem Dienst, mit dem Dienst verbundenen Server verbunden](scale/_static/azure-signalr-service-multiple-connections.png)

Dieser Ansatz für horizontales Skalieren hat mehrere Vorteile gegenüber der Redis-Rückwandplatine Alternative:

* Persistente Sitzungen, auch bekannt als [Clientaffinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), ist nicht erforderlich, da Clients sofort an den Azure SignalR Service umgeleitet werden, wenn sie eine Verbindung herstellen.
* Eine SignalR, die app horizontal hochskalieren kann, basierend auf der Anzahl der Nachrichten gesendet, während der Azure SignalR Service automatisch skaliert, um eine beliebige Anzahl von Verbindungen zu verarbeiten. Beispielsweise können Tausende von Clients vorhanden sein, aber wenn nur wenige Nachrichten pro Sekunde gesendet werden, die SignalR-app zum horizontalen hochskalieren auf mehrere Server, um die Verbindungen selbst behandeln muss nicht.
* Eine SignalR-app verwendet nicht wesentlich mehr Verbindungsressourcen als eine Web-app ohne SignalR.

Aus diesen Gründen empfehlen wir den Azure SignalR Service für alle ASP.NET Core SignalR-apps in Azure, einschließlich der App Service, VMs und Container gehostet.

Weitere Informationen finden Sie unter den [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis-Backplane

[Redis](https://redis.io/) ist ein in-Memory-Schlüssel-Wert-Speicher, der ein messaging-System mit einem Veröffentlichen/Abonnieren-Modell unterstützt. Die Redis-SignalR-Backplane verwendet das Pub/Sub-Feature, um Nachrichten an andere Server weiterzuleiten. Wenn ein Client eine Verbindung herstellt, wird die Verbindungsinformationen an der Rückwand übergeben. Wenn ein Server zum Senden einer Nachricht an alle Clients möchte, sendet sie an die Backplane. Der Rückwand weiß, alle verbundenen Clients und dem Server sind auf. Er sendet die Nachricht an alle Clients über ihre jeweiligen Server. Dieser Prozess wird im folgenden Diagramm dargestellt:

![Redis-Rückwandplatine, Nachricht, die von einem Server an alle Clients gesendet.](scale/_static/redis-backplane.png)

Die Redis-Rückwandplatine ist der empfohlene Ansatz für horizontales Skalieren für apps, die in Ihrer eigenen Infrastruktur gehostet werden. Der Azure SignalR Service ist eine praktische Option für die Produktion mit lokalen Anwendungen aufgrund der verbindungswartezeit zwischen Ihrem Datencenter und ein Azure-Rechenzentrum nicht.

Der Azure SignalR Service-Vorteile, die zuvor notierten sind Nachteile für die Redis-Rückwandplatine:

* Persistente Sitzungen, auch bekannt als [Clientaffinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), ist erforderlich. Sobald eine Verbindung auf einem Server initiiert wird, muss die Verbindung auf dem Server bleiben.
* Basierend auf der Anzahl von Clients muss eine SignalR-app skalieren, auch wenn einige Nachrichten gesendet werden.
* Eine SignalR-app verwendet deutlich mehr als eine Web-app ohne SignalR-Verbindungsressourcen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/signalr-overview)
* [Richten Sie eine Redis-Rückwandplatine](xref:signalr/redis-backplane)
