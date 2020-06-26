---
title: Cache-Taghilfsprogramm im ASP.NET Core MVC
author: pkellner
description: Erfahren Sie, wie das Cache-Taghilfsprogramm verwendet wird.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 65d8bbcdaed76a308b924ba024219e8f520bb585
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399282"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="8fcd6-103">Cache-Taghilfsprogramm im ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8fcd6-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="8fcd6-104">Von [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="8fcd6-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="8fcd6-105">Durch das Cache-Taghilfsprogramm kann die Leistung Ihrer ASP.NET Core-App verbessert werden, indem deren Inhalte im internen ASP.NET Core-Cacheanbieter zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="8fcd6-106">Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="8fcd6-107">Im folgenden Razor Markup wird das aktuelle Datum zwischengespeichert:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="8fcd6-108">Über die erste Anforderung an die Seite, die das Taghilfsprogramm enthält, wird das aktuelle Datum zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="8fcd6-109">Über zusätzliche Anforderungen werden die zwischengespeicherten Werte angezeigt, bis der Cache abläuft (standardmäßig 20 Minuten lang) oder bis das zwischengespeicherte Datum aus dem Cache entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="8fcd6-110">Attribute von Cache-Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="8fcd6-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="8fcd6-111">enabled</span><span class="sxs-lookup"><span data-stu-id="8fcd6-111">enabled</span></span>

| <span data-ttu-id="8fcd6-112">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-112">Attribute Type</span></span>  | <span data-ttu-id="8fcd6-113">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-113">Examples</span></span>        | <span data-ttu-id="8fcd6-114">Standard</span><span class="sxs-lookup"><span data-stu-id="8fcd6-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="8fcd6-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="8fcd6-115">Boolean</span></span>         | <span data-ttu-id="8fcd6-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="8fcd6-117">`enabled` legt fest, ob der Inhalt zwischengespeichert wird, der vom Cache-Taghilfsprogramm eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="8fcd6-118">Der Standardwert ist `true`.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-118">The default is `true`.</span></span> <span data-ttu-id="8fcd6-119">Wenn diese Option auf `false` festgelegt ist, wird die gerenderte Ausgabe **nicht** zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="8fcd6-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="8fcd6-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="8fcd6-121">expires-on</span></span>

| <span data-ttu-id="8fcd6-122">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-122">Attribute Type</span></span>   | <span data-ttu-id="8fcd6-123">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8fcd6-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="8fcd6-124">`expires-on` legt die absolute Ablaufzeit für das zwischengespeicherte Element fest.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="8fcd6-125">Im folgenden Beispiel werden die Inhalte des Cache-Taghilfsprogramms bis zum 29. Januar 2025 um 17:02 Uhr zwischengespeichert:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="8fcd6-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="8fcd6-126">expires-after</span></span>

| <span data-ttu-id="8fcd6-127">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-127">Attribute Type</span></span> | <span data-ttu-id="8fcd6-128">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8fcd6-128">Example</span></span>                      | <span data-ttu-id="8fcd6-129">Standard</span><span class="sxs-lookup"><span data-stu-id="8fcd6-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="8fcd6-130">20 Minuten</span><span class="sxs-lookup"><span data-stu-id="8fcd6-130">20 minutes</span></span> |

<span data-ttu-id="8fcd6-131">`expires-after` legt die Zeitspanne ab der ersten Anforderungszeit fest, um die Inhalte zwischenzuspeichern.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="8fcd6-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="8fcd6-133">Der Razor Standardwert wird von der Ansichts-Engine `expires-after` auf 20 Minuten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="8fcd6-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="8fcd6-134">expires-sliding</span></span>

| <span data-ttu-id="8fcd6-135">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-135">Attribute Type</span></span> | <span data-ttu-id="8fcd6-136">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8fcd6-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="8fcd6-137">Legt die Zeit fest, nach der ein Cacheeintrag gelöscht werden soll, wenn niemand auf diesen Wert zugegriffen hat.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="8fcd6-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="8fcd6-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="8fcd6-139">vary-by-header</span></span>

| <span data-ttu-id="8fcd6-140">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-140">Attribute Type</span></span> | <span data-ttu-id="8fcd6-141">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="8fcd6-142">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8fcd6-142">String</span></span>         | <span data-ttu-id="8fcd6-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="8fcd6-144">`vary-by-header` akzeptiert eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Cacheaktualisierung auslösen, wenn diese geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="8fcd6-145">Im folgenden Beispiel wird der Headerwert `User-Agent` überwacht.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="8fcd6-146">Außerdem werden die Inhalte für alle `User-Agent` zwischengespeichert, die dem Webserver präsentiert werden:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="8fcd6-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="8fcd6-147">vary-by-query</span></span>

| <span data-ttu-id="8fcd6-148">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-148">Attribute Type</span></span> | <span data-ttu-id="8fcd6-149">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="8fcd6-150">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8fcd6-150">String</span></span>         | <span data-ttu-id="8fcd6-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="8fcd6-152">`vary-by-query` akzeptiert eine durch Kommas getrennte Liste von <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in eine Abfragezeichenfolge (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), die eine Cacheaktualisierung auslösen, wenn der Wert eines aufgeführten Schlüssels geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="8fcd6-153">Im folgenden Beispiel werden die Werte von `Make` und `Model` überwacht.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="8fcd6-154">Außerdem werden die Inhalte für alle `Make` und `Model` zwischengespeichert, die dem Webserver präsentiert werden:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="8fcd6-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="8fcd6-155">vary-by-route</span></span>

| <span data-ttu-id="8fcd6-156">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-156">Attribute Type</span></span> | <span data-ttu-id="8fcd6-157">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="8fcd6-158">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8fcd6-158">String</span></span>         | <span data-ttu-id="8fcd6-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="8fcd6-160">`vary-by-route` akzeptiert eine durch Trennzeichen getrennte Liste mit Namen von Routenparametern, die eine Cacheaktualisierung auslösen, wenn der Routendatenparameter geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="8fcd6-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-161">Example:</span></span>

<span data-ttu-id="8fcd6-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="8fcd6-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="8fcd6-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="8fcd6-164">vary-by-cookie</span></span>

| <span data-ttu-id="8fcd6-165">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-165">Attribute Type</span></span> | <span data-ttu-id="8fcd6-166">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="8fcd6-167">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8fcd6-167">String</span></span>         | <span data-ttu-id="8fcd6-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="8fcd6-169">`vary-by-cookie` akzeptiert eine durch Trennzeichen getrennte Liste mit Namen von Cookies, die eine Cacheaktualisierung auslösen, wenn der Cookiewert geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="8fcd6-170">Im folgenden Beispiel wird das Cookie überwacht, das ASP.net Core zugeordnet ist Identity .</span><span class="sxs-lookup"><span data-stu-id="8fcd6-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="8fcd6-171">Wenn ein Benutzer authentifiziert wird, löst eine Änderung im Identity Cookie eine Cache Aktualisierung aus:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="8fcd6-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="8fcd6-172">vary-by-user</span></span>

| <span data-ttu-id="8fcd6-173">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-173">Attribute Type</span></span>  | <span data-ttu-id="8fcd6-174">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-174">Examples</span></span>        | <span data-ttu-id="8fcd6-175">Standard</span><span class="sxs-lookup"><span data-stu-id="8fcd6-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="8fcd6-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="8fcd6-176">Boolean</span></span>         | <span data-ttu-id="8fcd6-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="8fcd6-178">`vary-by-user` gibt an, ob der Cache zurückgesetzt wird, wenn sich ein anderer Benutzer anmeldet, also der Kontextprinzipal geändert wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="8fcd6-179">Der aktuelle Benutzer wird auch als Anforderungs Kontext Prinzipal bezeichnet und kann in einer Ansicht angezeigt werden, Razor indem auf verwiesen wird `@User.Identity.Name` .</span><span class="sxs-lookup"><span data-stu-id="8fcd6-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="8fcd6-180">Das folgende Beispiel überwacht den derzeit angemeldeten Benutzer, um eine Cacheaktualisierung auszulösen:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="8fcd6-181">Wenn Sie dieses Attribut verwenden, werden die Inhalte im Cache über einen Anmelde- und Abmeldezyklus verwaltet.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="8fcd6-182">Wenn der Wert auf `true` festgelegt wird, erklärt ein Authentifizierungszyklus den Cache für den authentifizierten Benutzer als ungültig.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="8fcd6-183">Der Cache wird für ungültig erklärt, da ein neuer eindeutiger Cookiewert bei der Anmeldung generiert wird, wenn ein Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="8fcd6-184">Der Cache wird für den Status „Anonym“ verwaltet, wenn kein Cookie vorhanden ist oder es abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="8fcd6-185">Wenn der Benutzer **nicht** authentifiziert ist, wird der Cache verwaltet.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="8fcd6-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="8fcd6-186">vary-by</span></span>

| <span data-ttu-id="8fcd6-187">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-187">Attribute Type</span></span> | <span data-ttu-id="8fcd6-188">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8fcd6-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="8fcd6-189">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="8fcd6-189">String</span></span>         | `@Model` |

<span data-ttu-id="8fcd6-190">Über `vary-by` können Sie festlegen, welche Daten zwischengespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="8fcd6-191">Wenn das Objekt verändert wird, auf das der Zeichenfolgenwert des Attributs verweist, wird der Inhalt des Cache-Hilfsprogramms aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="8fcd6-192">Häufig wird eine Zeichenfolgenverkettung von Modellwerten diesem Attribut zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="8fcd6-193">Dies führt letztlich zu einem Szenario, bei dem der Cache ungültig wird, wenn ein Update an einem der verketteten Werte vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="8fcd6-194">Bei dem folgenden Beispiel wird davon ausgegangen, dass die Controllermethode, die die Ansicht rendert, die ganzzahligen Werte der beiden Routenparameter `myParam1` und `myParam2` addiert, und die Summe als Modelleigenschaft zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="8fcd6-195">Wenn sich diese Summe ändert, wird auch der Inhalt des Cache-Taghilfsprogramms gerendert und erneut zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="8fcd6-196">Aktion:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="8fcd6-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="8fcd6-198">priority</span><span class="sxs-lookup"><span data-stu-id="8fcd6-198">priority</span></span>

| <span data-ttu-id="8fcd6-199">Attributtyp</span><span class="sxs-lookup"><span data-stu-id="8fcd6-199">Attribute Type</span></span>      | <span data-ttu-id="8fcd6-200">Beispiele</span><span class="sxs-lookup"><span data-stu-id="8fcd6-200">Examples</span></span>                               | <span data-ttu-id="8fcd6-201">Standard</span><span class="sxs-lookup"><span data-stu-id="8fcd6-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="8fcd6-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="8fcd6-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="8fcd6-203">`priority` enthält Anweisungen zum Entfernen des Caches für den integrierten Cacheanbieter.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="8fcd6-204">Der Webserver entfernt `Low`-Cacheeinträge erst, wenn der Arbeitsspeicher ausgelastet ist.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="8fcd6-205">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8fcd6-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="8fcd6-206">Das `priority`-Attribut garantiert keine festgelegte Ebene der Cachevermerkdauer.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="8fcd6-207">Bei `CacheItemPriority` handelt es sich nur um einen Vorschlag.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="8fcd6-208">Wenn Sie dieses Attribut auf `NeverRemove` festlegen, ist das noch keine Garantie dafür, dass zwischengespeicherte Elemente für immer gespeichert bleiben.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="8fcd6-209">Weitere Informationen finden Sie in den Themen im Abschnitt [Weitere Ressourcen](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="8fcd6-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="8fcd6-210">Das Cache-Taghilfsprogramm ist vom [Arbeitsspeicher Cache Service](xref:performance/caching/memory) abhängig.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="8fcd6-211">Das Cache-Taghilfsprogramm fügt den Dienst wenn nötig hinzu.</span><span class="sxs-lookup"><span data-stu-id="8fcd6-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8fcd6-212">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8fcd6-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
