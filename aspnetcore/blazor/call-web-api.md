---
title: Web-API aus ASP.net Core blazor aufzurufen
author: guardrex
description: Erfahren Sie, wie Sie eine Web-API über eine blazor-App mithilfe von JSON-Hilfsprogrammen aufzurufen, einschließlich der cors-Anforderungen (Cross-Origin Resource Sharing).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/call-web-api
ms.openlocfilehash: 2e79c81dc2371ef5b00f9251be47433a141c8ab7
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207140"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="ede12-103">Web-API aus ASP.net Core blazor aufzurufen</span><span class="sxs-lookup"><span data-stu-id="ede12-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="ede12-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ede12-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ede12-105">Blazor Webassembly-apps rufen Web-APIs mithilfe eines `HttpClient` vorkonfigurierten Diensts auf.</span><span class="sxs-lookup"><span data-stu-id="ede12-105">Blazor WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="ede12-106">Verfassen von Anforderungen, die JavaScript- [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Optionen mit blazor JSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage>enthalten können.</span><span class="sxs-lookup"><span data-stu-id="ede12-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="ede12-107">Blazor Server-apps greifen Web- <xref:System.Net.Http.HttpClient> APIs mithilfe von Instanzen <xref:System.Net.Http.IHttpClientFactory>an, die in der Regel mit</span><span class="sxs-lookup"><span data-stu-id="ede12-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="ede12-108">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="ede12-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="ede12-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ede12-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ede12-110">Beispiele für blazor Webassembly finden Sie unter den folgenden Komponenten in der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="ede12-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="ede12-111">Aufrufen der Web-API (*pages/callwebapi. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ede12-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="ede12-112">HTTP-Anforderungs Tester (*Components/httprequesttester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ede12-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="ede12-113">HttpClient-und JSON-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="ede12-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="ede12-114">In blazor Webassembly-Apps ist [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst zur Wiederherstellung von Anforderungen an den Ursprungsserver verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ede12-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="ede12-115">Um JSON-Hilfsprogramme zu verwenden `HttpClient` , fügen Sie einen Paket `Microsoft.AspNetCore.Blazor.HttpClient`Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="ede12-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="ede12-116">`HttpClient`und JSON-Hilfsprogramme werden auch verwendet, um Web-API-Endpunkte von Drittanbietern aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ede12-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="ede12-117">`HttpClient`wird mithilfe der Browser- [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) implementiert und unterliegt den Einschränkungen, einschließlich der Erzwingung derselben Ursprungs Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="ede12-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="ede12-118">Die Basisadresse des Clients wird auf die Adresse des Ursprungs Servers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ede12-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="ede12-119">Fügen Sie `HttpClient` eine-Instanz `@inject` mithilfe der-Direktive ein:</span><span class="sxs-lookup"><span data-stu-id="ede12-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="ede12-120">In den folgenden Beispielen verarbeitet eine TODO-Web-API Vorgänge zum Erstellen, lesen, aktualisieren und löschen (CRUD).</span><span class="sxs-lookup"><span data-stu-id="ede12-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="ede12-121">Die Beispiele basieren auf einer `TodoItem` Klasse, in der die-Klasse gespeichert wird:</span><span class="sxs-lookup"><span data-stu-id="ede12-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="ede12-122">ID (`Id`, `long`) &ndash; eindeutige ID des Elements.</span><span class="sxs-lookup"><span data-stu-id="ede12-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="ede12-123">Name (`Name`, `string`) &ndash; des Elements.</span><span class="sxs-lookup"><span data-stu-id="ede12-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="ede12-124">Status (`IsComplete`, `bool` )&ndash; gibt an, ob das TODO-Element abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="ede12-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="ede12-125">JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:</span><span class="sxs-lookup"><span data-stu-id="ede12-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="ede12-126">`GetJsonAsync`&ndash; Sendet eine HTTP GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ede12-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ede12-127">Im folgenden Code werden die `_todoItems` von der Komponente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ede12-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="ede12-128">Die `GetTodoItems` -Methode wird ausgelöst, wenn das Rendering der Komponente abgeschlossen ist ([oninitializedasync](xref:blazor/components#lifecycle-methods)).</span><span class="sxs-lookup"><span data-stu-id="ede12-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="ede12-129">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="ede12-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* <span data-ttu-id="ede12-130">`PostJsonAsync`&ndash; Sendet eine HTTP POST-Anforderung, einschließlich JSON-codiertem Inhalt, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ede12-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ede12-131">Im folgenden Code `_newItemName` wird von einem gebundenen Element der-Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ede12-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="ede12-132">Die `AddItem` -Methode wird durch Auswählen eines `<button>` -Elements ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ede12-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="ede12-133">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="ede12-133">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* <span data-ttu-id="ede12-134">`PutJsonAsync`&ndash; Sendet eine HTTP PUT-Anforderung, einschließlich JSON-codiertem Inhalt.</span><span class="sxs-lookup"><span data-stu-id="ede12-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="ede12-135">Im folgenden Code `_editItem` werden Werte für `Name` und von `IsCompleted` gebundenen Elementen der-Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ede12-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="ede12-136">Der Wert des `Id` Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzer `EditItem` Oberfläche ausgewählt und aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ede12-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="ede12-137">Die `SaveItem` -Methode wird ausgelöst, indem das `<button>` Save-Element ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="ede12-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="ede12-138">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="ede12-138">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="ede12-139"><xref:System.Net.Http>umfasst zusätzliche Erweiterungs Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="ede12-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="ede12-140">" [HttpClient. deleteasync](xref:System.Net.Http.HttpClient.DeleteAsync*) " wird verwendet, um eine HTTP DELETE-Anforderung an eine Web-API zu senden.</span><span class="sxs-lookup"><span data-stu-id="ede12-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="ede12-141">Im folgenden Code Ruft das DELETE `<button>` -Element die `DeleteItem` -Methode auf.</span><span class="sxs-lookup"><span data-stu-id="ede12-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="ede12-142">Das gebundene `<input>` Element stellt den `id` des zu löschenden Elements bereit.</span><span class="sxs-lookup"><span data-stu-id="ede12-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="ede12-143">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="ede12-143">See the sample app for a complete example.</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="ede12-144">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="ede12-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="ede12-145">Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bedient hat.</span><span class="sxs-lookup"><span data-stu-id="ede12-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="ede12-146">Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ede12-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="ede12-147">Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest.</span><span class="sxs-lookup"><span data-stu-id="ede12-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="ede12-148">Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu senden, muss der *Endpunkt* [Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/)aktivieren.</span><span class="sxs-lookup"><span data-stu-id="ede12-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="ede12-149">Die Beispiel-App veranschaulicht die Verwendung von cors in der Web-API-Aufruf Komponente (*pages/callwebapi. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ede12-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="ede12-150">Informationen dazu, wie Sie anderen Standorten cors-Anforderungen (Cross-Origin Resource Sharing) an Ihre <xref:security/cors>App senden können, finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="ede12-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="ede12-151">HttpClient und httprequestmessage mit Abruf-API-Anforderungs Optionen</span><span class="sxs-lookup"><span data-stu-id="ede12-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="ede12-152">Verwenden Sie zum Anpassen von Anforderungen bei der Ausführung in Webassembly in einer blazor Webassembly <xref:System.Net.Http.HttpRequestMessage> -APP [HttpClient](xref:fundamentals/http-requests) und zum Anpassen von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ede12-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="ede12-153">Sie können z. b. den Anforderungs-URI, die HTTP-Methode und alle gewünschten Anforderungs Header angeben.</span><span class="sxs-lookup"><span data-stu-id="ede12-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="ede12-154">Stellen Sie mithilfe der `WebAssemblyHttpMessageHandler.FetchArgs` -Eigenschaft für die Anforderung Anforderungs Optionen für die zugrunde liegende JavaScript-FETCH- [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) bereit.</span><span class="sxs-lookup"><span data-stu-id="ede12-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="ede12-155">Wie im folgenden Beispiel gezeigt, wird die `credentials` -Eigenschaft auf einen der folgenden Werte festgelegt:</span><span class="sxs-lookup"><span data-stu-id="ede12-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="ede12-156">`FetchCredentialsOption.Include`("include") &ndash; Weist den Browser an, Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) auch für Ursprungs übergreifende Anforderungen zu senden.</span><span class="sxs-lookup"><span data-stu-id="ede12-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="ede12-157">Nur zulässig, wenn die cors-Richtlinie für das Zulassen von Anmelde Informationen konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ede12-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="ede12-158">`FetchCredentialsOption.Omit`("weglassen") &ndash; Weist den Browser an, niemals Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) zu senden.</span><span class="sxs-lookup"><span data-stu-id="ede12-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="ede12-159">`FetchCredentialsOption.SameOrigin`("Gleiches-Ursprung") &ndash; Weist den Browser an, Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) nur dann zu senden, wenn sich die Ziel-URL auf demselben Ursprung befindet wie die aufrufende Anwendung.</span><span class="sxs-lookup"><span data-stu-id="ede12-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

```cshtml
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/todo"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="ede12-160">Weitere Informationen zu den Abruf-API-Optionen [finden Sie unter MDN-Webdokumentation: Windoworworkerglobalscope. FETCH ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="ede12-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="ede12-161">Beim Senden von Anmelde Informationen (Autorisierungs Cookies/-Header) für cors-Anforderungen muss der `Authorization` Header von der cors-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="ede12-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="ede12-162">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="ede12-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="ede12-163">Anforderungs Ursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ede12-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="ede12-164">Beliebige Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="ede12-164">Any method (verb).</span></span>
* <span data-ttu-id="ede12-165">`Content-Type`und `Authorization` -Header.</span><span class="sxs-lookup"><span data-stu-id="ede12-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="ede12-166">Um einen benutzerdefinierten Header (z `x-custom-header`. b.) zuzulassen, Listen Sie den-Header auf, wenn Sie aufrufen. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="ede12-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="ede12-167">Anmelde Informationen, die von Client seitigem JavaScript-`credentials` Code festgelegt `include`werden (Eigenschaft ist auf festgelegt)</span><span class="sxs-lookup"><span data-stu-id="ede12-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="ede12-168">Weitere Informationen finden <xref:security/cors> Sie unter und der Komponente "http-Anforderungs Tester" der Beispiel-app (*Components/httprequesttester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ede12-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ede12-169">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ede12-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* [<span data-ttu-id="ede12-170">Cross-Origin Resource Sharing (cors) bei W3C</span><span class="sxs-lookup"><span data-stu-id="ede12-170">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
