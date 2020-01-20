---
title: ASP.net Core Blazor Zustands Verwaltung
author: guardrex
description: Erfahren Sie, wie Sie den Status in Blazor Server-apps beibehalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: ffb32a4f274a30f2a5ceed9cbf193285e85bab4c
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160144"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="5322d-103">ASP.net Core Blazor Zustands Verwaltung</span><span class="sxs-lookup"><span data-stu-id="5322d-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="5322d-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="5322d-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="5322d-105"> Server ist ein Zustands behaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="5322d-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="5322d-106">In den meisten Fällen behält die APP eine laufende Verbindung mit dem Server bei.</span><span class="sxs-lookup"><span data-stu-id="5322d-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="5322d-107">Der Benutzer Zustand wird im Speicher des Servers *in einer Verbindung*gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5322d-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="5322d-108">Beispiele für den Zustand einer Benutzer Verbindung sind:</span><span class="sxs-lookup"><span data-stu-id="5322d-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="5322d-109">Die gerenderte Benutzeroberfläche&mdash;die Hierarchie von Komponenten Instanzen und deren neueste Renderausgabe.</span><span class="sxs-lookup"><span data-stu-id="5322d-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="5322d-110">Die Werte aller Felder und Eigenschaften in Komponenten Instanzen.</span><span class="sxs-lookup"><span data-stu-id="5322d-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="5322d-111">In [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection) -Dienst Instanzen gehaltene Daten, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="5322d-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="5322d-112">In diesem Artikel wird die Zustands Persistenz in Blazor Server-apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="5322d-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="5322d-113"> Webassembly-Apps können die [Client seitige Zustands Persistenz im Browser](#client-side-in-the-browser) nutzen, erfordern jedoch benutzerdefinierte Lösungen oder Drittanbieter Pakete, die den Rahmen dieses Artikels sprengen.</span><span class="sxs-lookup"><span data-stu-id="5322d-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="5322d-114"> Verbindungen</span><span class="sxs-lookup"><span data-stu-id="5322d-114"> circuits</span></span>

<span data-ttu-id="5322d-115">Wenn ein Benutzer einen vorübergehenden Netzwerk Verbindungsverlust erfährt, versucht Blazor, den Benutzer erneut mit der ursprünglichen Verbindung zu verbinden, damit die APP weiterhin verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="5322d-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="5322d-116">Es ist jedoch nicht immer möglich, einen Benutzer mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers wiederherzustellen:</span><span class="sxs-lookup"><span data-stu-id="5322d-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="5322d-117">Der Server kann eine getrennte Verbindung nicht ewig beibehalten.</span><span class="sxs-lookup"><span data-stu-id="5322d-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="5322d-118">Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.</span><span class="sxs-lookup"><span data-stu-id="5322d-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="5322d-119">In MultiServer-Bereitstellungs Umgebungen mit Lastenausgleich können alle Server Verarbeitungsanforderungen zu einem beliebigen Zeitpunkt nicht verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="5322d-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="5322d-120">Bei einzelnen Servern tritt möglicherweise ein Fehler auf, oder Sie werden automatisch entfernt, wenn die Gesamtmenge der Anforderungen nicht mehr verarbeitet werden muss.</span><span class="sxs-lookup"><span data-stu-id="5322d-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="5322d-121">Der ursprüngliche Server ist möglicherweise nicht verfügbar, wenn der Benutzer versucht, erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="5322d-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="5322d-122">Der Benutzer kann seinen Browser schließen und erneut öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="5322d-123">Beispielsweise gehen Werte, die durch JavaScript-Interop-Aufrufe festgelegt wurden, verloren.</span><span class="sxs-lookup"><span data-stu-id="5322d-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="5322d-124">Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand.</span><span class="sxs-lookup"><span data-stu-id="5322d-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="5322d-125">Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.</span><span class="sxs-lookup"><span data-stu-id="5322d-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="5322d-126">Status übergreifende Beibehaltung des Zustands</span><span class="sxs-lookup"><span data-stu-id="5322d-126">Preserve state across circuits</span></span>

<span data-ttu-id="5322d-127">In einigen Szenarios ist die Beibehaltung des Zustands über mehrere Verbindungen wünschenswert.</span><span class="sxs-lookup"><span data-stu-id="5322d-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="5322d-128">Eine APP kann wichtige Daten für einen Benutzer beibehalten, wenn Folgendes der folgenden ist:</span><span class="sxs-lookup"><span data-stu-id="5322d-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="5322d-129">Der Webserver steht nicht mehr zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5322d-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="5322d-130">Der Browser des Benutzers ist gezwungen, eine neue Verbindung mit einem neuen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="5322d-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="5322d-131">Im Allgemeinen gilt: die Zustands übergreifende Zustands Verwaltung gilt für Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="5322d-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="5322d-132">*Speichern Sie die Daten nicht nur im Speicher des Servers*, um den Status außerhalb einer einzelnen Verbindung beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="5322d-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="5322d-133">Die APP muss die Daten an einem anderen Speicherort speichern.</span><span class="sxs-lookup"><span data-stu-id="5322d-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="5322d-134">Die Zustands Persistenz erfolgt nicht automatisch&mdash;Sie müssen bei der Entwicklung der APP Schritte ausführen, um Zustands behaftete Daten Persistenz zu implementieren</span><span class="sxs-lookup"><span data-stu-id="5322d-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="5322d-135">Die Daten Persistenz ist in der Regel nur für einen hochwertigen Status erforderlich, den Benutzer für die Erstellung aufgewendet haben.</span><span class="sxs-lookup"><span data-stu-id="5322d-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="5322d-136">In den folgenden Beispielen spart die Beibehaltung des Zustands entweder Zeit oder Hilfsmittel in kommerziellen Aktivitäten:</span><span class="sxs-lookup"><span data-stu-id="5322d-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="5322d-137">Mehrstufige Webform &ndash; es für einen Benutzer zeitaufwändig ist, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Prozesses erneut einzugeben, wenn deren Status verloren geht.</span><span class="sxs-lookup"><span data-stu-id="5322d-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="5322d-138">Ein Benutzer verliert den Zustand in diesem Szenario, wenn er vom mehrstufigen Formular weg navigiert und später zum Formular zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="5322d-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="5322d-139">Einkaufswagen &ndash; eine kommerziell wichtige Komponente einer APP, die einen potenziellen Umsatz darstellt.</span><span class="sxs-lookup"><span data-stu-id="5322d-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="5322d-140">Ein Benutzer, der seinen Zustand verliert und somit seinen Warenkorb, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="5322d-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="5322d-141">Es ist in der Regel nicht notwendig, den einfach neu erstellten Zustand beizubehalten, z. b. den Benutzernamen, der in ein Anmelde Dialogfeld eingegeben wurde, das nicht übermittelt wurde.</span><span class="sxs-lookup"><span data-stu-id="5322d-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5322d-142">Eine APP kann nur den *App-Zustand*persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="5322d-142">An app can only persist *app state*.</span></span> <span data-ttu-id="5322d-143">UIs können nicht persistent gespeichert werden, z. b. Komponenten Instanzen und deren Rendering-Strukturen.</span><span class="sxs-lookup"><span data-stu-id="5322d-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="5322d-144">Komponenten und Rendering-Strukturen sind in der Regel nicht serialisierbar.</span><span class="sxs-lookup"><span data-stu-id="5322d-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="5322d-145">Um eine ähnliche Situation wie die erweiterten Knoten einer TreeView zu erhalten, muss die APP über benutzerdefinierten Code verfügen, um das Verhalten als serialisierbaren App-Zustand zu modellieren.</span><span class="sxs-lookup"><span data-stu-id="5322d-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="5322d-146">Speicherort des Zustands</span><span class="sxs-lookup"><span data-stu-id="5322d-146">Where to persist state</span></span>

<span data-ttu-id="5322d-147">Es gibt drei allgemeine Speicherorte für die Beibehaltung des Zustands in einer Blazor Server-app.</span><span class="sxs-lookup"><span data-stu-id="5322d-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="5322d-148">Jeder Ansatz eignet sich am besten für verschiedene Szenarien und hat unterschiedliche Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="5322d-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="5322d-149">Server seitig in einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="5322d-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="5322d-150">URL</span><span class="sxs-lookup"><span data-stu-id="5322d-150">URL</span></span>](#url)
* [<span data-ttu-id="5322d-151">Client seitig im Browser</span><span class="sxs-lookup"><span data-stu-id="5322d-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="5322d-152">Server seitig in einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="5322d-152">Server-side in a database</span></span>

<span data-ttu-id="5322d-153">Für permanente Daten Persistenz oder für Daten, die mehrere Benutzer oder Geräte umfassen müssen, ist eine unabhängige serverseitige Datenbank fast sicherlich die beste Wahl.</span><span class="sxs-lookup"><span data-stu-id="5322d-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="5322d-154">Zu den Optionen gehören:</span><span class="sxs-lookup"><span data-stu-id="5322d-154">Options include:</span></span>

* <span data-ttu-id="5322d-155">Relationale SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="5322d-155">Relational SQL database</span></span>
* <span data-ttu-id="5322d-156">Schlüssel-Wert-Speicherung</span><span class="sxs-lookup"><span data-stu-id="5322d-156">Key-value store</span></span>
* <span data-ttu-id="5322d-157">BLOB-Speicher</span><span class="sxs-lookup"><span data-stu-id="5322d-157">Blob store</span></span>
* <span data-ttu-id="5322d-158">Tabellen Speicher</span><span class="sxs-lookup"><span data-stu-id="5322d-158">Table store</span></span>

<span data-ttu-id="5322d-159">Nachdem die Daten in der Datenbank gespeichert wurden, kann eine neue Verbindung jederzeit von einem Benutzer gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="5322d-160">Die Daten des Benutzers werden aufbewahrt und sind in jeder neuen Verbindung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5322d-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="5322d-161">Weitere Informationen zu den Azure-Datenspeicher Optionen finden Sie in der [Azure Storage-Dokumentation](/azure/storage/) und [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="5322d-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="5322d-162">URL</span><span class="sxs-lookup"><span data-stu-id="5322d-162">URL</span></span>

<span data-ttu-id="5322d-163">Modellieren Sie die Daten bei vorübergehenden Daten, die den Navigations Zustand darstellen, als Teil der URL.</span><span class="sxs-lookup"><span data-stu-id="5322d-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="5322d-164">Zu den in der URL modellierten Zuständen zählen beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="5322d-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="5322d-165">Die ID einer angezeigten Entität.</span><span class="sxs-lookup"><span data-stu-id="5322d-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="5322d-166">Die aktuelle Seitenzahl in einem Auslagerungs Raster.</span><span class="sxs-lookup"><span data-stu-id="5322d-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="5322d-167">Der Inhalt der Adressleiste des Browsers wird beibehalten:</span><span class="sxs-lookup"><span data-stu-id="5322d-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="5322d-168">, Wenn der Benutzer die Seite manuell neu lädt.</span><span class="sxs-lookup"><span data-stu-id="5322d-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="5322d-169">Wenn der Webserver nicht mehr verfügbar ist&mdash;wird der Benutzer gezwungen, die Seite erneut zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5322d-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="5322d-170">Weitere Informationen zum Definieren von URL-Mustern mit der `@page`-Direktive finden Sie unter <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="5322d-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="5322d-171">Client seitig im Browser</span><span class="sxs-lookup"><span data-stu-id="5322d-171">Client-side in the browser</span></span>

<span data-ttu-id="5322d-172">Bei vorübergehenden Daten, die der Benutzer aktiv erstellt, sind die `localStorage`-und `sessionStorage` Sammlungen des Browsers ein gängiger Sicherungs Speicher.</span><span class="sxs-lookup"><span data-stu-id="5322d-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="5322d-173">Die APP muss den gespeicherten Zustand nicht verwalten oder löschen, wenn die Verbindung abgebrochen wird. Dies ist ein Vorteil gegenüber Server seitigem Speicher.</span><span class="sxs-lookup"><span data-stu-id="5322d-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="5322d-174">"Client seitig" bezieht sich in diesem Abschnitt auf Client seitige Szenarios im Browser, nicht auf das [Blazor Webassembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="5322d-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="5322d-175">`localStorage` und `sessionStorage` können in Blazor Webassembly-Apps verwendet werden, jedoch nur durch das Schreiben von benutzerdefiniertem Code oder das Verwenden eines Drittanbieter Pakets.</span><span class="sxs-lookup"><span data-stu-id="5322d-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="5322d-176">`localStorage` und `sessionStorage` unterscheiden sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5322d-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="5322d-177">`localStorage` ist auf den Browser des Benutzers beschränkt.</span><span class="sxs-lookup"><span data-stu-id="5322d-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="5322d-178">Wenn der Benutzer die Seite erneut lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="5322d-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="5322d-179">Wenn der Benutzer mehrere Browser Registerkarten öffnet, wird der Status auf den Registerkarten freigegeben.</span><span class="sxs-lookup"><span data-stu-id="5322d-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="5322d-180">Daten bleiben in `localStorage` bestehen, bis Sie explizit gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="5322d-181">`sessionStorage` ist auf die Registerkarte Browser des Benutzers festgelegt. Wenn der Benutzer die Registerkarte erneut lädt, bleibt der Zustand erhalten.</span><span class="sxs-lookup"><span data-stu-id="5322d-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="5322d-182">Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Status verloren.</span><span class="sxs-lookup"><span data-stu-id="5322d-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="5322d-183">Wenn der Benutzer mehrere Browser Registerkarten öffnet, verfügt jede Registerkarte über eine eigene unabhängige Version der Daten.</span><span class="sxs-lookup"><span data-stu-id="5322d-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="5322d-184">Im Allgemeinen ist `sessionStorage` sicherer zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5322d-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="5322d-185">`sessionStorage` vermeidet das Risiko, dass ein Benutzer mehrere Registerkarten öffnet und auf Folgendes stößt:</span><span class="sxs-lookup"><span data-stu-id="5322d-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="5322d-186">Fehler in Zustands Speicher zwischen Registerkarten.</span><span class="sxs-lookup"><span data-stu-id="5322d-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="5322d-187">Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt.</span><span class="sxs-lookup"><span data-stu-id="5322d-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="5322d-188">`localStorage` ist die bessere Wahl, wenn die APP den Zustand über schließen beibehalten und den Browser erneut öffnen muss.</span><span class="sxs-lookup"><span data-stu-id="5322d-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="5322d-189">Einschränkungen für die Verwendung von Browser Speicher:</span><span class="sxs-lookup"><span data-stu-id="5322d-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="5322d-190">Ähnlich wie bei der Verwendung einer serverseitigen Datenbank sind das Laden und Speichern von Daten asynchron.</span><span class="sxs-lookup"><span data-stu-id="5322d-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="5322d-191">Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während der vorab Ausführung nicht verfügbar, da die angeforderte Seite im Browser während der vorab Erstellungsphase nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5322d-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="5322d-192">Die Speicherung von einigen Kilobyte an Daten kann für Blazor Server-apps dauerhaft gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="5322d-193">Über einige Kilobytes hinaus müssen Sie die Auswirkungen auf die Leistung in Erwägung gezogen, da die Daten im Netzwerk geladen und gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="5322d-194">Benutzer können die Daten anzeigen oder manipulieren.</span><span class="sxs-lookup"><span data-stu-id="5322d-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="5322d-195">ASP.net Core [Datenschutz](xref:security/data-protection/introduction) kann das Risiko mindern.</span><span class="sxs-lookup"><span data-stu-id="5322d-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="5322d-196">Browser Speicherlösungen von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="5322d-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="5322d-197">Nuget-Pakete von Drittanbietern stellen APIs zum Arbeiten mit `localStorage` und `sessionStorage`bereit.</span><span class="sxs-lookup"><span data-stu-id="5322d-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="5322d-198">Es lohnt sich zu erwägen, ein Paket auszuwählen, das den [Datenschutz](xref:security/data-protection/introduction)ASP.net Core transparent verwendet.</span><span class="sxs-lookup"><span data-stu-id="5322d-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5322d-199">ASP.net Core Datenschutz verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko der Manipulation gespeicherter Daten.</span><span class="sxs-lookup"><span data-stu-id="5322d-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="5322d-200">Wenn JSON-serialisierte Daten als Klartext gespeichert werden, können Benutzer die Daten mithilfe von Browser Entwicklertools anzeigen und auch die gespeicherten Daten ändern.</span><span class="sxs-lookup"><span data-stu-id="5322d-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="5322d-201">Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind.</span><span class="sxs-lookup"><span data-stu-id="5322d-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="5322d-202">Beispielsweise ist das Lesen oder Ändern der gespeicherten Farbe eines UI-Elements für den Benutzer oder die Organisation kein erhebliches Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="5322d-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="5322d-203">Vermeiden Sie es Benutzern, *sensible Daten*zu überprüfen oder zu manipulieren.</span><span class="sxs-lookup"><span data-stu-id="5322d-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="5322d-204">Experimentelles Paket für geschützten Browser Speicher</span><span class="sxs-lookup"><span data-stu-id="5322d-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="5322d-205">Ein Beispiel für ein nuget-Paket, das den [Schutz von Daten](xref:security/data-protection/introduction) für `localStorage` und `sessionStorage` bietet, ist [Microsoft. aspnetcore. protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="5322d-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="5322d-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das zurzeit für die Verwendung in der Produktion ungeeignet ist.</span><span class="sxs-lookup"><span data-stu-id="5322d-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="5322d-207">Installation</span><span class="sxs-lookup"><span data-stu-id="5322d-207">Installation</span></span>

<span data-ttu-id="5322d-208">So installieren Sie das `Microsoft.AspNetCore.ProtectedBrowserStorage` Paket:</span><span class="sxs-lookup"><span data-stu-id="5322d-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="5322d-209">Fügen Sie im Blazor Server-App-Projekt einen Paket Verweis auf [Microsoft. aspnetcore. protectedbrowserstorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)hinzu.</span><span class="sxs-lookup"><span data-stu-id="5322d-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="5322d-210">Fügen Sie im HTML-Code der obersten Ebene (z. b. in der Datei *pages/_Host. cshtml* in der Standard Projektvorlage) das folgende `<script>` Tag hinzu:</span><span class="sxs-lookup"><span data-stu-id="5322d-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="5322d-211">Nennen Sie in der `Startup.ConfigureServices`-Methode `AddProtectedBrowserStorage`, um der Dienst Sammlung `localStorage` und `sessionStorage` Dienste hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5322d-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="5322d-212">Speichern und Laden von Daten in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="5322d-212">Save and load data within a component</span></span>

<span data-ttu-id="5322d-213">Verwenden Sie [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) in einer Komponente, die das Laden oder Speichern von Daten in den Browser Speicher erfordert, eine Instanz von einem der folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="5322d-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="5322d-214">Die Auswahl hängt von dem Sicherungs Speicher ab, den Sie verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="5322d-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="5322d-215">Im folgenden Beispiel wird `sessionStorage` verwendet:</span><span class="sxs-lookup"><span data-stu-id="5322d-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="5322d-216">Die `@using`-Anweisung kann in einer *_Imports. Razor* -Datei statt in der-Komponente abgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="5322d-217">Durch die Verwendung der Datei " *_Imports. Razor* " wird der Namespace für größere Segmente der APP oder der gesamten App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="5322d-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="5322d-218">Um den `currentCount` Wert in der `Counter`-Komponente der Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync`verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5322d-218">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="5322d-219">Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario.</span><span class="sxs-lookup"><span data-stu-id="5322d-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="5322d-220">Apps werden wahrscheinlich ganze Modell Objekte speichern, die einen komplexen Zustand enthalten.</span><span class="sxs-lookup"><span data-stu-id="5322d-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="5322d-221">die JSON-Daten werden von `ProtectedSessionStore` automatisch serialisiert und deserialisiert.</span><span class="sxs-lookup"><span data-stu-id="5322d-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="5322d-222">Im vorangehenden Codebeispiel werden die `currentCount` Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5322d-222">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="5322d-223">Die Daten werden nicht als Klartext gespeichert, sondern durch ASP.net Core [Datenschutz](xref:security/data-protection/introduction)geschützt.</span><span class="sxs-lookup"><span data-stu-id="5322d-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5322d-224">Die verschlüsselten Daten können angezeigt werden, wenn `sessionStorage['count']` in der Entwickler Konsole des Browsers ausgewertet wird.</span><span class="sxs-lookup"><span data-stu-id="5322d-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="5322d-225">Um die `currentCount` Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter` Komponente zurückkehrt (auch wenn Sie sich in einer vollständig neuen Verbindung befinden), verwenden Sie `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="5322d-225">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="5322d-226">Wenn die Parameter der Komponente den Navigations Zustand einschließen, wenden Sie `ProtectedSessionStore.GetAsync` an, und weisen Sie das Ergebnis in `OnParametersSetAsync`, nicht `OnInitializedAsync`zu.</span><span class="sxs-lookup"><span data-stu-id="5322d-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="5322d-227">`OnInitializedAsync` wird nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="5322d-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="5322d-228">`OnInitializedAsync` wird später nicht erneut aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf derselben Seite verbleibt.</span><span class="sxs-lookup"><span data-stu-id="5322d-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="5322d-229">Weitere Informationen finden Sie unter <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="5322d-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="5322d-230">Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server keine vorab Aktivierung aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="5322d-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="5322d-231">Wenn die vorab Aktivierung aktiviert ist, wird ein Fehler ähnlich dem folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="5322d-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="5322d-232">JavaScript-Interop-Aufrufe können zu diesem Zeitpunkt nicht ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="5322d-233">Dies liegt daran, dass die Komponente vorab in eine Vorabversion übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="5322d-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="5322d-234">Deaktivieren Sie entweder die vorab Ausführung, oder fügen Sie zusätzlichen Code hinzu, um mit der vorab Ausführung zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="5322d-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="5322d-235">Weitere Informationen zum Schreiben von Code, der mit der vorab Generierung funktioniert, finden Sie im Abschnitt [handle-vorab](#handle-prerendering) Version.</span><span class="sxs-lookup"><span data-stu-id="5322d-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="5322d-236">Behandeln des Ladezustands</span><span class="sxs-lookup"><span data-stu-id="5322d-236">Handle the loading state</span></span>

<span data-ttu-id="5322d-237">Da der Browser Speicher asynchron ist (auf den über eine Netzwerkverbindung zugegriffen wird), gibt es immer einen Zeitraum, bis die Daten geladen sind und zur Verwendung durch eine Komponente verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="5322d-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="5322d-238">Um die besten Ergebnisse zu erzielen, sollten Sie während des Ladens eine Ladezustands Meldung erstellen, anstatt leere oder Standarddaten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5322d-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="5322d-239">Ein Ansatz besteht darin, zu überprüfen, ob die Daten `null` (noch geladen) sind oder nicht.</span><span class="sxs-lookup"><span data-stu-id="5322d-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="5322d-240">In der Standard `Counter` Komponente wird die Anzahl in einem `int`gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5322d-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="5322d-241">Legen Sie `currentCount` Nullwerte fest, indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5322d-241">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="5322d-242">Anstatt die Schaltfläche Anzahl und **Inkrement** bedingungslos anzuzeigen, können Sie diese Elemente nur anzeigen, wenn die Daten geladen werden:</span><span class="sxs-lookup"><span data-stu-id="5322d-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="5322d-243">Vorab Rendering</span><span class="sxs-lookup"><span data-stu-id="5322d-243">Handle prerendering</span></span>

<span data-ttu-id="5322d-244">Während der Vorab Anmeldung:</span><span class="sxs-lookup"><span data-stu-id="5322d-244">During prerendering:</span></span>

* <span data-ttu-id="5322d-245">Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="5322d-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="5322d-246">Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5322d-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="5322d-247">`localStorage` oder `sessionStorage` sind während der vorab Generierung nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5322d-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="5322d-248">Wenn die Komponente versucht, mit dem Speicher zu interagieren, wird ein Fehler ähnlich dem folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="5322d-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="5322d-249">JavaScript-Interop-Aufrufe können zu diesem Zeitpunkt nicht ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5322d-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="5322d-250">Dies liegt daran, dass die Komponente vorab in eine Vorabversion übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="5322d-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="5322d-251">Eine Möglichkeit, den Fehler zu beheben, besteht darin, die vorab Generierung zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="5322d-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="5322d-252">Dies ist normalerweise die beste Wahl, wenn die APP den browserbasierten Speicher stark nutzt.</span><span class="sxs-lookup"><span data-stu-id="5322d-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="5322d-253">Das vorab Rendering erhöht die Komplexität und profitiert von der APP nicht, da die APP keine nützlichen Inhalte vorab bereitstellen kann, bis `localStorage` oder `sessionStorage` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="5322d-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="5322d-254">Öffnen Sie zum Deaktivieren der vorab Generierung die Datei *pages/_Host. cshtml* , und ändern Sie den `render-mode` des `Component`-taghilfsprogramms in `Server`.</span><span class="sxs-lookup"><span data-stu-id="5322d-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the call to `render-mode` of the `Component` Tag Helper to `Server`.</span></span>

<span data-ttu-id="5322d-255">Die vorab Generierung ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage`nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="5322d-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="5322d-256">Verschieben Sie den Ladevorgang so lange, bis der Browser mit der Verbindung verbunden ist, um die vorab Ausführung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="5322d-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="5322d-257">Im folgenden finden Sie ein Beispiel für das Speichern eines Leistungs Zählers:</span><span class="sxs-lookup"><span data-stu-id="5322d-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="5322d-258">Einen allgemeinen Speicherort für die Zustands Beibehaltung berücksichtigen</span><span class="sxs-lookup"><span data-stu-id="5322d-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="5322d-259">Wenn viele Komponenten auf Browser basiertem Speicher basieren, wird durch erneutes Implementieren des Zustands Anbieter Codes die Code Duplizierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="5322d-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="5322d-260">Eine Möglichkeit zur Vermeidung von Code Duplizierung besteht darin, eine über *geordnete Zustands Anbieter Komponente* zu erstellen, die die Zustands Anbieter Logik kapselt.</span><span class="sxs-lookup"><span data-stu-id="5322d-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="5322d-261">Untergeordnete Komponenten können ohne Berücksichtigung des Zustands Persistenzmechanismus mit beibehaltenen Daten arbeiten.</span><span class="sxs-lookup"><span data-stu-id="5322d-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="5322d-262">Im folgenden Beispiel für eine `CounterStateProvider` Komponente werden die Daten des Zählers persistent gespeichert:</span><span class="sxs-lookup"><span data-stu-id="5322d-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="5322d-263">Die `CounterStateProvider` Komponente verarbeitet die Lade Phase, indem der untergeordnete Inhalt erst nach Abschluss des Ladens gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5322d-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="5322d-264">Um die `CounterStateProvider` Komponente zu verwenden, schließen Sie eine Instanz der Komponente in eine beliebige andere Komponente ein, die Zugriff auf den Gegenstand benötigt.</span><span class="sxs-lookup"><span data-stu-id="5322d-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="5322d-265">Um den Status für alle Komponenten in einer APP zugänglich zu machen, wrappen Sie die `CounterStateProvider` Komponente um die `Router` in der `App` Komponente (*app. Razor*):</span><span class="sxs-lookup"><span data-stu-id="5322d-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="5322d-266">Umschließende Komponenten empfangen und können den persistenten Status des Zählers ändern.</span><span class="sxs-lookup"><span data-stu-id="5322d-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="5322d-267">Die folgende `Counter` Komponente implementiert das Muster:</span><span class="sxs-lookup"><span data-stu-id="5322d-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="5322d-268">Die vorherige Komponente ist nicht erforderlich, um mit `ProtectedBrowserStorage`zu interagieren, auch nicht mit einer "Lade Phase".</span><span class="sxs-lookup"><span data-stu-id="5322d-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="5322d-269">Um die vorab Ausführung wie oben beschrieben zu behandeln, können `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die die Daten des Zählers nutzen, automatisch mit der vorab Ausführung arbeiten.</span><span class="sxs-lookup"><span data-stu-id="5322d-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="5322d-270">Weitere Informationen finden Sie im Abschnitt Vorabversion des [Handles](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="5322d-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="5322d-271">Im Allgemeinen wird ein über *geordnetes Zustands Anbieter-Komponenten* Muster empfohlen:</span><span class="sxs-lookup"><span data-stu-id="5322d-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="5322d-272">, Um den Zustand in vielen anderen Komponenten zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5322d-272">To consume state in many other components.</span></span>
* <span data-ttu-id="5322d-273">, Wenn nur ein Zustands Objekt der obersten Ebene beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="5322d-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="5322d-274">Es ist besser, das Laden und Speichern des Zustands Global zu vermeiden, um viele verschiedene Zustands Objekte beizubehalten und verschiedene Teilmengen von Objekten an unterschiedlichen Stellen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5322d-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
