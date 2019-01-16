---
title: Verwenden der React-Redux-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für React-Redux und create-react-app vertraut machen.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react-with-redux
ms.openlocfilehash: c1aedcf1e14a9e7b339b60dd02c4267cd5945a49
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341619"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>Verwenden der React-Redux-Projektvorlage mit ASP.NET Core

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> In dieser Dokumentation beziehen nicht auf die React mit Redux-Projektvorlage in ASP.NET Core 2.0 enthaltenen. Er bezieht sich auf die neuere React mit Redux-Vorlage, die Sie manuell aktualisieren. Die Vorlage ist in ASP.NET Core 2.1 oder höher verfügbar.

::: moniker-end

Die aktualisierte React-with-Redux-Projektvorlage stellt einen nützlichen Ausgangspunkt für ASP.NET Core-Apps mit Redux, React und [CRA-Konventionen ](https://github.com/facebookincubator/create-react-app) (Create-React-App) dar, um eine umfassende clientseitige Benutzeroberfläche (UI) zu implementieren.


Mit Ausnahme des Befehls zur Projekterstellung sind alle Informationen zur React-with-Redux-Vorlage identisch mit jenen zur React-Vorlage. Führen Sie zum Erstellen dieses Projekttyps `dotnet new reactredux` anstelle von `dotnet new react` aus. Weitere Informationen zu den Funktionen für die beiden React-basierten Vorlagen finden Sie in der [Dokumentation zur React-Vorlage(](xref:spa/react).

Informationen zum Konfigurieren von einem React mit Redux unteranwendung in IIS finden Sie unter ["reactredux" Vorlage 2.1: Keine SPA in IIS zu verwenden (Aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).
