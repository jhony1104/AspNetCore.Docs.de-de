---
title: Blazor-Zustandsverwaltung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie den Zustand in Blazor-Server-Apps beibehalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 59adcce972b503a6aa6e596bc9bff63225961f84
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243199"
---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="a839b-103">Blazor-Zustandsverwaltung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a839b-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="a839b-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="a839b-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="a839b-105"> Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="a839b-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="a839b-106">In den meisten Fällen behält die App eine laufende Verbindung mit dem Server bei.</span><span class="sxs-lookup"><span data-stu-id="a839b-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="a839b-107">Der Benutzerzustand wird in einer *Verbindung* im Speicher des Servers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a839b-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="a839b-108">Beispiele für den Zustand einer Benutzerverbindung sind:</span><span class="sxs-lookup"><span data-stu-id="a839b-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="a839b-109">Die gerenderte Benutzeroberfläche: die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe.</span><span class="sxs-lookup"><span data-stu-id="a839b-109">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="a839b-110">Die Werte aller Felder und Eigenschaften in Komponenteninstanzen.</span><span class="sxs-lookup"><span data-stu-id="a839b-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="a839b-111">Daten, die in [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection)-Dienstinstanzen gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="a839b-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="a839b-112">In diesem Artikel wird die Zustandspersistenz in Blazor Server-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="a839b-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="a839b-113">-Webassemblyanwendungen können [clientseitige Zustandspersistenz im Browser nutzen](#client-side-in-the-browser), erfordern jedoch benutzerdefinierte Lösungen oder Drittanbieterpakete, die den Rahmen dieses Artikels sprengen würden.</span><span class="sxs-lookup"><span data-stu-id="a839b-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="a839b-114">-Verbindungen</span><span class="sxs-lookup"><span data-stu-id="a839b-114"> circuits</span></span>

<span data-ttu-id="a839b-115">Wenn die Netzwerkverbindung vorübergehend getrennt wird, versucht Blazor, den Benutzer nochmals mit der ursprünglichen Verbindung zu verbinden, damit er die mit der Verwendung der App fortfahren kann.</span><span class="sxs-lookup"><span data-stu-id="a839b-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="a839b-116">Es ist jedoch nicht immer möglich, einen Benutzer noch mal mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers zu verbinden:</span><span class="sxs-lookup"><span data-stu-id="a839b-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="a839b-117">Der Server kann eine getrennte Verbindung nicht dauerhaft beibehalten.</span><span class="sxs-lookup"><span data-stu-id="a839b-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="a839b-118">Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.</span><span class="sxs-lookup"><span data-stu-id="a839b-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="a839b-119">In Bereitstellungsumgebungen mit mehreren Servern und Lastenausgleich kann es vorkommen, dass ein Server, der Anforderungen verarbeitet, zu einem bestimmten Zeitpunkt nicht mehr verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="a839b-120">Bei einzelnen Servern tritt möglicherweise ein Fehler auf, oder sie werden automatisch entfernt, wenn nicht mehr das gesamte Anforderungsvolumen verarbeitet werden muss.</span><span class="sxs-lookup"><span data-stu-id="a839b-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="a839b-121">Der ursprüngliche Server ist möglicherweise nicht verfügbar, wenn der Benutzer versucht, noch mal eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="a839b-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="a839b-122">Der Benutzer kann den Browser schließen und noch mal öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="a839b-123">So gehen beispielsweise Werte, die durch JavaScript-Interoperabilitätsaufrufe festgelegt wurden, verloren.</span><span class="sxs-lookup"><span data-stu-id="a839b-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="a839b-124">Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand.</span><span class="sxs-lookup"><span data-stu-id="a839b-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="a839b-125">Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.</span><span class="sxs-lookup"><span data-stu-id="a839b-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="a839b-126">Beibehaltung von zustandsübergreifenden Verbindungen</span><span class="sxs-lookup"><span data-stu-id="a839b-126">Preserve state across circuits</span></span>

<span data-ttu-id="a839b-127">In einigen Szenarios ist die Beibehaltung des Zustands über mehrere Verbindungen wünschenswert.</span><span class="sxs-lookup"><span data-stu-id="a839b-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="a839b-128">Eine App kann in folgenden Fällen wichtige Daten für einen Benutzer beibehalten:</span><span class="sxs-lookup"><span data-stu-id="a839b-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="a839b-129">Der Webserver ist nicht mehr verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a839b-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="a839b-130">Der Browser des Benutzers ist gezwungen, eine neue Verbindung mit einem neuen Webserver zu starten.</span><span class="sxs-lookup"><span data-stu-id="a839b-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="a839b-131">Im Allgemeinen gilt: die Beibehaltung des Zustands über mehrere Verbindungen hinweg erfolgt in Szenarios, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="a839b-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="a839b-132">Um den Zustand über eine einzelne Verbindung hinaus beizubehalten, *speichern Sie die Daten nicht nur im Arbeitsspeicher des Servers*.</span><span class="sxs-lookup"><span data-stu-id="a839b-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="a839b-133">Die App muss die Daten an einem anderen Speicherort speichern.</span><span class="sxs-lookup"><span data-stu-id="a839b-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="a839b-134">Die Zustandspersistenz erfolgt nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="a839b-134">State persistence isn't automatic.</span></span> <span data-ttu-id="a839b-135">Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="a839b-135">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="a839b-136">Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von den Benutzern mit großem Aufwand erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="a839b-136">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="a839b-137">In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:</span><span class="sxs-lookup"><span data-stu-id="a839b-137">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="a839b-138">Mehrstufiges Webformular: Es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Prozesses wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-138">Multistep webform: It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="a839b-139">Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom mehrstufigen Formular weg navigiert und später zum Formular zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="a839b-139">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="a839b-140">Warenkorb: Kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-140">Shopping cart: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="a839b-141">Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="a839b-141">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="a839b-142">Es ist in der Regel nicht notwendig, einen Zustand beizubehalten, der einfach erneut erstellt werden kann, z. B. den in ein Anmeldedialogfeld eingegebenen Benutzernamen, der nicht übermittelt wurde.</span><span class="sxs-lookup"><span data-stu-id="a839b-142">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a839b-143">Eine App kann nur den *App-Zustand* beibehalten.</span><span class="sxs-lookup"><span data-stu-id="a839b-143">An app can only persist *app state*.</span></span> <span data-ttu-id="a839b-144">Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-144">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="a839b-145">Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar.</span><span class="sxs-lookup"><span data-stu-id="a839b-145">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="a839b-146">Um eine Art von Benutzeroberflächenzustand, z. B. von den erweiterten Knoten einer Strukturansicht, zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten als serialisierbaren App-Zustand modellieren zu können.</span><span class="sxs-lookup"><span data-stu-id="a839b-146">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="a839b-147">Speicherort des Zustands</span><span class="sxs-lookup"><span data-stu-id="a839b-147">Where to persist state</span></span>

<span data-ttu-id="a839b-148">Es gibt drei allgemeine Speicherorte für die Beibehaltung des Zustands in einer Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="a839b-148">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="a839b-149">Die Ansätze eignen sich jeweils für verschiedene Szenarios und weisen unterschiedliche Einschränkungen auf:</span><span class="sxs-lookup"><span data-stu-id="a839b-149">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="a839b-150">Serverseitig in einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="a839b-150">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="a839b-151">URL</span><span class="sxs-lookup"><span data-stu-id="a839b-151">URL</span></span>](#url)
* [<span data-ttu-id="a839b-152">Clientseitig im Browser</span><span class="sxs-lookup"><span data-stu-id="a839b-152">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="a839b-153">Serverseitig in einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="a839b-153">Server-side in a database</span></span>

<span data-ttu-id="a839b-154">Für die dauerhafte Beibehaltung von Daten oder für Daten, die mehrere Benutzer oder Geräte einbeziehen müssen, ist eine unabhängige serverseitige Datenbank in den meisten Fällen die beste Lösung.</span><span class="sxs-lookup"><span data-stu-id="a839b-154">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="a839b-155">Zu den Optionen gehören:</span><span class="sxs-lookup"><span data-stu-id="a839b-155">Options include:</span></span>

* <span data-ttu-id="a839b-156">Relationale SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="a839b-156">Relational SQL database</span></span>
* <span data-ttu-id="a839b-157">Schlüssel-Wert-Speicherung</span><span class="sxs-lookup"><span data-stu-id="a839b-157">Key-value store</span></span>
* <span data-ttu-id="a839b-158">Blob-Speicher</span><span class="sxs-lookup"><span data-stu-id="a839b-158">Blob store</span></span>
* <span data-ttu-id="a839b-159">Tabellenspeicher</span><span class="sxs-lookup"><span data-stu-id="a839b-159">Table store</span></span>

<span data-ttu-id="a839b-160">Nachdem die Daten in der Datenbank gespeichert wurden, kann der Benutzer jederzeit eine neue Verbindung starten.</span><span class="sxs-lookup"><span data-stu-id="a839b-160">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="a839b-161">Die Daten des Benutzers werden beibehalten und sind in jeder neuen Verbindung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a839b-161">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="a839b-162">Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie in der [Azure Storage-Dokumentation](/azure/storage/) und [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="a839b-162">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="a839b-163">URL</span><span class="sxs-lookup"><span data-stu-id="a839b-163">URL</span></span>

<span data-ttu-id="a839b-164">Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL.</span><span class="sxs-lookup"><span data-stu-id="a839b-164">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="a839b-165">Beispiele für in der URL modellierte Zustände:</span><span class="sxs-lookup"><span data-stu-id="a839b-165">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="a839b-166">Die ID einer angezeigten Entität.</span><span class="sxs-lookup"><span data-stu-id="a839b-166">The ID of a viewed entity.</span></span>
* <span data-ttu-id="a839b-167">Die aktuelle Seitenzahl in einem ausgelagerten Raster.</span><span class="sxs-lookup"><span data-stu-id="a839b-167">The current page number in a paged grid.</span></span>

<span data-ttu-id="a839b-168">Der Inhalt der Adressleiste des Browsers wird in folgenden Fällen beibehalten:</span><span class="sxs-lookup"><span data-stu-id="a839b-168">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="a839b-169">Der Benutzer lädt die Seite nochmals manuell.</span><span class="sxs-lookup"><span data-stu-id="a839b-169">If the user manually reloads the page.</span></span>
* <span data-ttu-id="a839b-170">Wenn der Webserver nicht mehr verfügbar ist, ist der Benutzer gezwungen, die Seite nochmals zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a839b-170">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="a839b-171">Weitere Informationen zum Definieren von URL-Mustern mit der `@page`-Direktive finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a839b-171">For information on defining URL patterns with the `@page` directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="a839b-172">Clientseitig im Browser</span><span class="sxs-lookup"><span data-stu-id="a839b-172">Client-side in the browser</span></span>

<span data-ttu-id="a839b-173">Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die `localStorage`- und `sessionStorage`-Sammlungen des Browsers ein häufig verwendeter Sicherungsspeicher.</span><span class="sxs-lookup"><span data-stu-id="a839b-173">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="a839b-174">Die App muss den gespeicherten Zustand nicht verwalten oder löschen, wenn die Verbindung abgebrochen wird. Dies ist ein Vorteil gegenüber serverseitigem Speicher.</span><span class="sxs-lookup"><span data-stu-id="a839b-174">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="a839b-175">„Clientseitig“ bezieht sich in diesem Abschnitt auf clientseitige Szenarios im Browser, nicht auf das [Blazor-Webassembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="a839b-175">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="a839b-176">`localStorage` und `sessionStorage` können in Blazor-Webassembly-Apps verwendet werden, jedoch nur durch Schreiben von benutzerdefiniertem Code oder Verwenden eines Drittanbieterpakets.</span><span class="sxs-lookup"><span data-stu-id="a839b-176">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="a839b-177">`localStorage` und `sessionStorage` unterscheiden sich wie folgt:</span><span class="sxs-lookup"><span data-stu-id="a839b-177">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="a839b-178">`localStorage` bezieht sich auf den Browser des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="a839b-178">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="a839b-179">Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="a839b-179">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="a839b-180">Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben.</span><span class="sxs-lookup"><span data-stu-id="a839b-180">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="a839b-181">Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-181">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="a839b-182">`sessionStorage` bezieht sich auf die Registerkarte des Browsers des Benutzers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="a839b-182">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="a839b-183">Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren.</span><span class="sxs-lookup"><span data-stu-id="a839b-183">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="a839b-184">Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.</span><span class="sxs-lookup"><span data-stu-id="a839b-184">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="a839b-185">Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer.</span><span class="sxs-lookup"><span data-stu-id="a839b-185">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="a839b-186">`sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:</span><span class="sxs-lookup"><span data-stu-id="a839b-186">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="a839b-187">Fehler in Zustandsspeicher über mehrere Registerkarten hinweg</span><span class="sxs-lookup"><span data-stu-id="a839b-187">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="a839b-188">Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt</span><span class="sxs-lookup"><span data-stu-id="a839b-188">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="a839b-189">`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.</span><span class="sxs-lookup"><span data-stu-id="a839b-189">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="a839b-190">Einschränkungen bei der Verwendung des Browserspeichers:</span><span class="sxs-lookup"><span data-stu-id="a839b-190">Caveats for using browser storage:</span></span>

* <span data-ttu-id="a839b-191">Ähnlich wie bei der Verwendung einer serverseitigen Datenbank erfolgt das Laden und Speichern von Daten asynchron.</span><span class="sxs-lookup"><span data-stu-id="a839b-191">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="a839b-192">Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während des Prerenderings nicht verfügbar, da die angeforderte Seite im Browser während der Prerenderingphase nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-192">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="a839b-193">Es ist ohne Weiteres möglich, einige wenige Kilobyte Daten für Blazor Server-Apps zu speichern.</span><span class="sxs-lookup"><span data-stu-id="a839b-193">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="a839b-194">Bei mehr Kilobyte müssen Beeinträchtigungen der Leistung berücksichtigt werden, da die Daten über das Netzwerk geladen und gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-194">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="a839b-195">Benutzer können die Daten anzeigen oder manipulieren.</span><span class="sxs-lookup"><span data-stu-id="a839b-195">Users may view or tamper with the data.</span></span> <span data-ttu-id="a839b-196">Die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core reduziert die damit verbundenen Risiken.</span><span class="sxs-lookup"><span data-stu-id="a839b-196">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="a839b-197">Browserspeicherlösungen von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="a839b-197">Third-party browser storage solutions</span></span>

<span data-ttu-id="a839b-198">NuGet-Pakete von Drittanbietern stellen APIs für die Verwendung mit `localStorage` und `sessionStorage` bereit.</span><span class="sxs-lookup"><span data-stu-id="a839b-198">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="a839b-199">Erwägen Sie die Auswahl eines Pakets, das die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core transparent verwendet.</span><span class="sxs-lookup"><span data-stu-id="a839b-199">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a839b-200">Die Datenschutzlösung von ASP.NET Core verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko, dass gespeicherte Daten manipuliert werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-200">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="a839b-201">Wenn JSON-serialisierte Daten als Klartext gespeichert werden, können Benutzer die Daten mithilfe von Browserentwicklertools anzeigen und auch die gespeicherten Daten ändern.</span><span class="sxs-lookup"><span data-stu-id="a839b-201">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="a839b-202">Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind.</span><span class="sxs-lookup"><span data-stu-id="a839b-202">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="a839b-203">Beispielsweise stellt das Lesen oder Ändern der gespeicherten Farbe eines Benutzeroberflächenelements für den Benutzer oder die Organisation kein ernsthaftes Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="a839b-203">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="a839b-204">Vermeiden Sie, dass Benutzer *sensible Daten* überprüfen oder manipulieren können.</span><span class="sxs-lookup"><span data-stu-id="a839b-204">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="a839b-205">Experimentelles Paket für geschützten Browserspeicher</span><span class="sxs-lookup"><span data-stu-id="a839b-205">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="a839b-206">Ein Beispiel für ein NuGet-Paket, das [Datenschutz](xref:security/data-protection/introduction) für `localStorage` und `sessionStorage` bietet, ist [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="a839b-206">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="a839b-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das derzeit nicht für den Einsatz in der Produktion geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-207">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="a839b-208">Installation</span><span class="sxs-lookup"><span data-stu-id="a839b-208">Installation</span></span>

<span data-ttu-id="a839b-209">So installieren Sie das `Microsoft.AspNetCore.ProtectedBrowserStorage`-Paket</span><span class="sxs-lookup"><span data-stu-id="a839b-209">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="a839b-210">Fügen Sie im Blazor Server-App-Projekt einen Paketverweis zu [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) hinzu.</span><span class="sxs-lookup"><span data-stu-id="a839b-210">In the Blazor Server app project, add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="a839b-211">Fügen Sie im HTML-Code der obersten Ebene (z. B. in der Datei `Pages/_Host.cshtml` in der Standardprojektvorlage) das folgende `<script>`-Tag hinzu:</span><span class="sxs-lookup"><span data-stu-id="a839b-211">In the top-level HTML (for example, in the `Pages/_Host.cshtml` file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="a839b-212">Rufen Sie für die `Startup.ConfigureServices`-Methode `AddProtectedBrowserStorage` auf, um der Dienstsammlung die Dienste `localStorage` und `sessionStorage` hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="a839b-212">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="a839b-213">Speichern und Laden von Daten in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="a839b-213">Save and load data within a component</span></span>

<span data-ttu-id="a839b-214">Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, [`@inject`](xref:mvc/views/razor#inject), um eine Instanz von einem der folgenden Komponenten einzufügen:</span><span class="sxs-lookup"><span data-stu-id="a839b-214">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:mvc/views/razor#inject) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="a839b-215">Die Auswahl hängt von dem Sicherungsspeicher ab, den Sie verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="a839b-215">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="a839b-216">Im folgenden Beispiel wird `sessionStorage` verwendet:</span><span class="sxs-lookup"><span data-stu-id="a839b-216">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="a839b-217">Die `@using`-Anweisung kann in einer `_Imports.razor`-Datei statt in der Komponente abgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-217">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="a839b-218">Bei Verwendung der `_Imports.razor`-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="a839b-218">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="a839b-219">Um den `currentCount`-Wert in der `Counter`-Komponente der Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a839b-219">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="a839b-220">Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario.</span><span class="sxs-lookup"><span data-stu-id="a839b-220">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="a839b-221">Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten.</span><span class="sxs-lookup"><span data-stu-id="a839b-221">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="a839b-222">`ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch.</span><span class="sxs-lookup"><span data-stu-id="a839b-222">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="a839b-223">Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a839b-223">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="a839b-224">Die Daten werden nicht im Klartext gespeichert, sondern mit der [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core geschützt.</span><span class="sxs-lookup"><span data-stu-id="a839b-224">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="a839b-225">Die verschlüsselten Daten können angezeigt werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.</span><span class="sxs-lookup"><span data-stu-id="a839b-225">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="a839b-226">Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer vollständig neuen Verbindung befindet):</span><span class="sxs-lookup"><span data-stu-id="a839b-226">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="a839b-227">Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie das Ergebnis in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> und nicht in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zu.</span><span class="sxs-lookup"><span data-stu-id="a839b-227">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="a839b-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird nur einmal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="a839b-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="a839b-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt.</span><span class="sxs-lookup"><span data-stu-id="a839b-229"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="a839b-230">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="a839b-230">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="a839b-231">Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-231">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="a839b-232">Wenn Prerendering aktiviert ist, wird ein Fehler ähnlich dem folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="a839b-232">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="a839b-233">JavaScript-Interop-Aufrufe können derzeit nicht durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-233">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="a839b-234">Dies liegt daran, dass die Komponente vorab gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="a839b-234">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="a839b-235">Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="a839b-235">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="a839b-236">Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a839b-236">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="a839b-237">Handhabung des Ladezustands</span><span class="sxs-lookup"><span data-stu-id="a839b-237">Handle the loading state</span></span>

<span data-ttu-id="a839b-238">Da die Browserspeicherung asynchron erfolgt (Zugriff über eine Netzwerkverbindung), vergeht immer eine gewisse Zeit, bis die Daten geladen und für die Verwendung durch eine Komponente verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="a839b-238">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="a839b-239">Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a839b-239">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="a839b-240">Ein Ansatz besteht darin, zu beobachten, ob die Daten den Zustand `null` aufweisen (werden noch geladen) oder nicht.</span><span class="sxs-lookup"><span data-stu-id="a839b-240">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="a839b-241">In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a839b-241">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="a839b-242">Legen Sie fest, dass `currentCount` NULL-Werte aufweisen können, indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="a839b-242">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="a839b-243">Anstatt bedingungslos die Anzahl und die Schaltfläche **`Increment`** anzuzeigen, legen Sie fest, dass diese Elemente nur dann angezeigt werden, wenn die Daten geladen sind:</span><span class="sxs-lookup"><span data-stu-id="a839b-243">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="a839b-244">Handhabung von Prerendering</span><span class="sxs-lookup"><span data-stu-id="a839b-244">Handle prerendering</span></span>

<span data-ttu-id="a839b-245">Während des Prerenderings:</span><span class="sxs-lookup"><span data-stu-id="a839b-245">During prerendering:</span></span>

* <span data-ttu-id="a839b-246">Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="a839b-246">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="a839b-247">Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a839b-247">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="a839b-248">`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a839b-248">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="a839b-249">Wenn die Komponente versucht, mit dem Speicher zu interagieren, wird ein Fehler ähnlich dem folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="a839b-249">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="a839b-250">JavaScript-Interop-Aufrufe können derzeit nicht durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-250">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="a839b-251">Dies liegt daran, dass die Komponente vorab gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="a839b-251">This is because the component is being prerendered.</span></span>

<span data-ttu-id="a839b-252">Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a839b-252">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="a839b-253">Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt.</span><span class="sxs-lookup"><span data-stu-id="a839b-253">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="a839b-254">Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="a839b-254">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="a839b-255">Öffnen Sie die Datei `Pages/_Host.cshtml`, und ändern Sie die `render-mode`-Eigenschaft des [Komponententag-Hilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um das Prerendering zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a839b-255">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="a839b-256">Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="a839b-256">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="a839b-257">Damit das Prerendering aktiviert bleibt, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="a839b-257">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="a839b-258">Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:</span><span class="sxs-lookup"><span data-stu-id="a839b-258">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="a839b-259">Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort</span><span class="sxs-lookup"><span data-stu-id="a839b-259">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="a839b-260">Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes.</span><span class="sxs-lookup"><span data-stu-id="a839b-260">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="a839b-261">Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen*, die die Zustandsanbieterlogik kapselt.</span><span class="sxs-lookup"><span data-stu-id="a839b-261">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="a839b-262">Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.</span><span class="sxs-lookup"><span data-stu-id="a839b-262">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="a839b-263">Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="a839b-263">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

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

<span data-ttu-id="a839b-264">Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="a839b-264">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="a839b-265">Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt.</span><span class="sxs-lookup"><span data-stu-id="a839b-265">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="a839b-266">Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den <xref:Microsoft.AspNetCore.Components.Routing.Router> in der `App`-Komponente (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="a839b-266">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="a839b-267">Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern.</span><span class="sxs-lookup"><span data-stu-id="a839b-267">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="a839b-268">Die folgende `Counter`-Komponente implementiert das Muster:</span><span class="sxs-lookup"><span data-stu-id="a839b-268">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="a839b-269">Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a839b-269">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="a839b-270">Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten.</span><span class="sxs-lookup"><span data-stu-id="a839b-270">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="a839b-271">Ausführliche Informationen finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="a839b-271">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="a839b-272">In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:</span><span class="sxs-lookup"><span data-stu-id="a839b-272">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="a839b-273">Der Zustand soll in vielen anderen Komponenten verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="a839b-273">To consume state in many other components.</span></span>
* <span data-ttu-id="a839b-274">Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="a839b-274">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="a839b-275">Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das Laden und Speichern von Zustandsobjekten nicht global zu handhaben.</span><span class="sxs-lookup"><span data-stu-id="a839b-275">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
