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
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="f342d-103">Verwenden der React-Redux-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f342d-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="f342d-104">In dieser Dokumentation beziehen nicht auf die React mit Redux-Projektvorlage in ASP.NET Core 2.0 enthaltenen.</span><span class="sxs-lookup"><span data-stu-id="f342d-104">This documentation doesn't pertain to the React-with-Redux project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="f342d-105">Er bezieht sich auf die neuere React mit Redux-Vorlage, die Sie manuell aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f342d-105">It pertains to the newer React-with-Redux template that you can update manually.</span></span> <span data-ttu-id="f342d-106">Die Vorlage ist in ASP.NET Core 2.1 oder höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f342d-106">The template is available in ASP.NET Core 2.1 or later.</span></span>

::: moniker-end

<span data-ttu-id="f342d-107">Die aktualisierte React-with-Redux-Projektvorlage stellt einen nützlichen Ausgangspunkt für ASP.NET Core-Apps mit Redux, React und [CRA-Konventionen ](https://github.com/facebookincubator/create-react-app) (Create-React-App) dar, um eine umfassende clientseitige Benutzeroberfläche (UI) zu implementieren.
</span><span class="sxs-lookup"><span data-stu-id="f342d-107">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="f342d-108">Mit Ausnahme des Befehls zur Projekterstellung sind alle Informationen zur React-with-Redux-Vorlage identisch mit jenen zur React-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="f342d-108">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="f342d-109">Führen Sie zum Erstellen dieses Projekttyps `dotnet new reactredux` anstelle von `dotnet new react` aus.</span><span class="sxs-lookup"><span data-stu-id="f342d-109">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="f342d-110">Weitere Informationen zu den Funktionen für die beiden React-basierten Vorlagen finden Sie in der [Dokumentation zur React-Vorlage(](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="f342d-110">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="f342d-111">Informationen zum Konfigurieren von einem React mit Redux unteranwendung in IIS finden Sie unter ["reactredux" Vorlage 2.1: Keine SPA in IIS zu verwenden (Aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="f342d-111">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
