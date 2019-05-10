---
title: Kompatibilitätsversion für ASP.NET Core MVC
author: rick-anderson
description: Informationen zur Vorgehensweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/15/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b360da105799a1dccb1902e167e50e78864b76a9
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085885"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="67111-103">Kompatibilitätsversion für ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="67111-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="67111-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="67111-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="67111-105">Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen.</span><span class="sxs-lookup"><span data-stu-id="67111-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span> <span data-ttu-id="67111-106">Diese potentiell Fehler verursachenden Änderungen im Verhalten betreffen generell das Verhalten des MVC-Subsystems und die Art, wie **Ihr Code** von der Runtime aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="67111-106">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="67111-107">Wenn Sie sich für die Änderungen entscheiden, erhalten Sie das aktuelle Verhalten und das langfristige Verhalten von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67111-107">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="67111-108">Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest:</span><span class="sxs-lookup"><span data-stu-id="67111-108">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="67111-109">Es wird empfohlen, Ihre App mit der aktuellen Version zu testen (`CompatibilityVersion.Version_2_2`).</span><span class="sxs-lookup"><span data-stu-id="67111-109">We recommend you test your app using the latest version (`CompatibilityVersion.Version_2_2`).</span></span> <span data-ttu-id="67111-110">Wir erwarten, dass bei den meisten Apps mit der aktuellen Version keine Fehler verursachenden Verhaltensänderungen auftreten werden.</span><span class="sxs-lookup"><span data-stu-id="67111-110">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="67111-111">Apps, die `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` aufrufen, sind vor potentiell Fehler verursachenden Änderungen im Verhalten geschützt, die in ASP.NET Core 2.1 MVC und höheren 2.x-Versionen eingeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="67111-111">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1 MVC and later 2.x versions.</span></span> <span data-ttu-id="67111-112">Dieser Schutz:</span><span class="sxs-lookup"><span data-stu-id="67111-112">This protection:</span></span>

* <span data-ttu-id="67111-113">Gilt nicht für alle Änderungen in 2.1 und höher. Das Ziel sind potentiell Fehler verursachende Änderungen im Verhalten der ASP.NET Core-Runtime im MVC-Subsystem.</span><span class="sxs-lookup"><span data-stu-id="67111-113">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="67111-114">Erstreckt sich nicht auf die nächste Hauptversion.</span><span class="sxs-lookup"><span data-stu-id="67111-114">Does not extend to the next major version.</span></span>

<span data-ttu-id="67111-115">Die Standard-Kompatibilität für ASP.NET Core 2.1 und höhere 2.x-Aps, die **nicht** `SetCompatibilityVersion` aufrufen, ist 2.0-Kompatibilität.</span><span class="sxs-lookup"><span data-stu-id="67111-115">The default compatibility for ASP.NET Core 2.1 and later 2.x apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="67111-116">Das bedeutet, `SetCompatibilityVersion` nicht aufzurufen entspricht dem Aufrufen von `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="67111-116">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="67111-117">Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest, außer für die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="67111-117">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="67111-118">Bei Apps, bei denen Fehler verursachende Änderungen auftreten, können Sie die geeigneten Kompatibilitätsoptionen verwenden, um Folgendes zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="67111-118">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="67111-119">Sie können das aktuelle Release verwenden und spezifische Fehler verursachende Änderungen im Verhalten vermeiden.</span><span class="sxs-lookup"><span data-stu-id="67111-119">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="67111-120">Sie bekommen die Gelegenheit, Ihre App zu aktualisieren, damit Sie mit den neuesten Änderungen funktioniert.</span><span class="sxs-lookup"><span data-stu-id="67111-120">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="67111-121">In der <xref:Microsoft.AspNetCore.Mvc.MvcOptions>-Dokumentation finden Sie eine gute Erklärung, was sich geändert hat und warum die Änderungen eine Verbesserung für die meisten Benutzer darstellen.</span><span class="sxs-lookup"><span data-stu-id="67111-121">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="67111-122">Zu einem späteren Zeitpunkt wird es eine [ASP.NET Core 3.0-Version](https://github.com/aspnet/Home/wiki/Roadmap) geben.</span><span class="sxs-lookup"><span data-stu-id="67111-122">At some future date, there will be an [ASP.NET Core 3.0 version](https://github.com/aspnet/Home/wiki/Roadmap).</span></span> <span data-ttu-id="67111-123">Altes Verhalten, das von Kompatibilitätsoptionen unterstützt wird, wird in der 3.0-Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="67111-123">Old behaviors supported by compatibility switches will be removed in the 3.0 version.</span></span> <span data-ttu-id="67111-124">Beinahe alle Benutzer werden von diesen positiven Änderungen profitieren.</span><span class="sxs-lookup"><span data-stu-id="67111-124">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="67111-125">Dadurch, dass die Änderungen bereits jetzt eingeführt werden, können die meisten Apps auch jetzt davon profitieren, und die anderen bekommen Zeit, ihre Apps zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="67111-125">By introducing these changes now, most apps can benefit now, and the others will have time to update their apps.</span></span>
