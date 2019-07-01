---
title: Wählen zwischen ASP.NET 4.x und ASP.NET Core
author: rick-anderson
description: Erklärt ASP.NET Core vs. ASP.NET 4.x und wie man sich für eines von beiden entscheidet.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/02/2019
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 97d65df8df1e27694d818098958288874e8ab92e
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67399168"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Wählen zwischen ASP.NET 4.x und ASP.NET Core

ASP.NET Core ist eine Neugestaltung von ASP.NET 4.x. Dieser Artikel listet die Unterschiede auf.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core ist ein plattformübergreifendes Open-Source-Framework zum Erstellen moderner, cloudbasierter Web-Apps unter Windows, macOS oder Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x ist ein ausgereiftes Framework, das sämtliche Dienste bietet, die zum Erstellen erstklassiger serverbasierter Web-Apps unter Windows für Unternehmen erforderlich sind.

## <a name="framework-selection"></a>Auswahl des Frameworks

Die folgende Tabelle vergleicht ASP.NET Core mit ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Entwickeln für Windows, macOS oder Linux|Entwickeln für Windows|
|[Razor-Seiten](xref:razor-pages/index) werden für das Erstellen einer Webbenutzeroberfläche mit ASP.NET Core 2.x empfohlen. Weitere Informationen finden Sie unter [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api) und [SignalR](xref:signalr/introduction).|Verwenden von [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/) oder [WebHooks](/aspnet/webhooks/) oder [Web Pages](/aspnet/web-pages)|
|Mehrere Versionen pro Computer|Eine Version pro Computer|
|Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) oder [Visual Studio Code](https://code.visualstudio.com/) unter Verwendung von C# oder F#|Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/) unter Verwendung von C#, VB oder F#|
|Höhere Leistung als ASP.NET 4.x|Gute Leistung|
|[Wählen der .NET Framework- oder .NET Core-Laufzeit](/dotnet/standard/choosing-core-framework-server)|Verwenden der .NET Framework-Laufzeit|

Weitere Informationen über Unterstützung für ASP.NET Core 2.x in .NET Framework finden Sie unter [ASP.NET Core targeting .NET Framework](xref:index#target-framework).

## <a name="aspnet-core-scenarios"></a>ASP.NET Core-Szenarien

* [Websites](xref:tutorials/first-mvc-app/index)
* [APIs](xref:tutorials/first-web-api)
* [Echtzeit](xref:signalr/index)
* [Bereitstellen einer ASP.NET Core-App in Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x-Szenarios

* [Websites](/aspnet/mvc)
* [APIs](/aspnet/web-api)
* [Echtzeit](/aspnet/signalr)
* [Erstellen einer ASP.NET 4.x-Web-App in Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Einführung in ASP.NET](/aspnet/overview)
* [Einführung in ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
