---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: Verwenden von OWIN zum Selfhosten von ASP.NET-Web-API | Microsoft-Dokumentation
author: rick-anderson
description: In diesem Tutorial wird gezeigt, wie ASP.NET Web-API in einer Konsolenanwendung, die mithilfe von OWIN zum selfhosten der Web-API-Framework gehostet wird. Öffnen Sie die Web Interface for .NET (OWIN) d...
ms.author: riande
ms.date: 07/09/2013
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: 59ce24aa47ca590fbe9b617dbbe8bc6b3711849e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667387"
---
<a name="use-owin-to-self-host-aspnet-web-api"></a>Verwenden von OWIN zum Selfhosten von ASP.NET-Web-API 
====================

> In diesem Tutorial wird gezeigt, wie ASP.NET Web-API in einer Konsolenanwendung, die mithilfe von OWIN zum selfhosten der Web-API-Framework gehostet wird.
>
> [Öffnen von Weboberfläche für .NET](http://owin.org) (OWIN) definiert eine Abstraktion zwischen Webservern für .NET und Webanwendungen. OWIN entkoppelt die Webanwendung aus dem Server, wodurch OWIN ideal für das Selbsthosting einer Webanwendung in Ihrem eigenen Prozess, außerhalb von IIS.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 
> - Web-API 5.2.7


> [!NOTE]
> Sie finden den vollständigen Quellcode für dieses Tutorial auf [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).


## <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

Auf der **Datei** Menü **neu**, und wählen Sie dann **Projekt**. Von **installiert**unter **Visual C#** Option **Windows Desktop** und wählen Sie dann **Konsolen-App (.Net Framework)**. Nennen Sie das Projekt "OwinSelfhostSample", und wählen Sie **OK**.

[![](use-owin-to-self-host-web-api/_static/image7.png)](use-owin-to-self-host-web-api/_static/image7.png)

## <a name="add-the-web-api-and-owin-packages"></a>Fügen Sie die Web-API und OWIN-Pakete

Von der **Tools** , wählen Sie im Menü **NuGet Package Manager**, und wählen Sie dann **-Paket-Manager-Konsole**. Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

Hiermit wird dem WebAPI-OWIN-Selfhost-Paket und alle erforderlichen Pakete der OWIN-installiert.

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a>Konfigurieren von Web-API für die Self-Hosting

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen** / **Klasse** eine neue Klasse hinzufügen. Nennen Sie die Klasse `Startup`.

![](use-owin-to-self-host-web-api/_static/image5.png)

Ersetzen Sie alle die Codebausteine in dieser Datei durch Folgendes:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a>Hinzufügen eines Web-API-Controllers

Als Nächstes fügen Sie eine Web-API-Controller-Klasse hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen** / **Klasse** eine neue Klasse hinzufügen. Nennen Sie die Klasse `ValuesController`.

Ersetzen Sie alle die Codebausteine in dieser Datei durch Folgendes:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-with-httpclient"></a>Starten Sie die OWIN-Host, und stellen Sie eine Anforderung mit "HttpClient"

Ersetzen Sie alle die Codebausteine in der Datei "Program.cs" durch Folgendes:

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Um die Anwendung auszuführen, drücken Sie F5 in Visual Studio aus. Die Ausgabe sollte wie folgt aussehen:

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Übersicht über das Katana-Projekt](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[Hosten von ASP.NET Web-API in einer Azure-Workerrolle](host-aspnet-web-api-in-an-azure-worker-role.md)
