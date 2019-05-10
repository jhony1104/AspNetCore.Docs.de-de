---
title: Verwenden der React-Redux-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für React-Redux und create-react-app vertraut machen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react-with-redux
ms.openlocfilehash: ed2e9aea449ddb09fef049a391f40f57452786a8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893687"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="08270-103">Verwenden der React-Redux-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08270-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="08270-104">Die aktualisierte React-with-Redux-Projektvorlage stellt einen nützlichen Ausgangspunkt für ASP.NET Core-Apps mit Redux, React und [CRA-Konventionen ](https://github.com/facebookincubator/create-react-app) (Create-React-App) dar, um eine umfassende clientseitige Benutzeroberfläche (UI) zu implementieren.
</span><span class="sxs-lookup"><span data-stu-id="08270-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="08270-105">Mit Ausnahme des Befehls zur Projekterstellung sind alle Informationen zur React-with-Redux-Vorlage identisch mit jenen zur React-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="08270-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="08270-106">Führen Sie zum Erstellen dieses Projekttyps `dotnet new reactredux` anstelle von `dotnet new react` aus.</span><span class="sxs-lookup"><span data-stu-id="08270-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="08270-107">Weitere Informationen zu den Funktionen für die beiden React-basierten Vorlagen finden Sie in der [Dokumentation zur React-Vorlage(](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="08270-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="08270-108">Informationen zum Konfigurieren von einem React mit Redux unteranwendung in IIS finden Sie unter ["reactredux" Vorlage 2.1: Keine SPA in IIS zu verwenden (Aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="08270-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
