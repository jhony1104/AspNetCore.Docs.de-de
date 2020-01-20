---
title: Ruft eine Web-API aus ASP.net Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine Web-API aus einer Blazor-App mithilfe von JSON-Hilfsprogrammen aufzurufen, einschließlich der cors-Anforderungen (Cross-Origin Resource Sharing).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 66605f38a6fcaedebc92b0946dca1e5f28b593c6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160066"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="7d5da-103">Ruft eine Web-API aus ASP.net Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7d5da-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="7d5da-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)und [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="7d5da-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7d5da-105">[Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) -apps Web-APIs mithilfe eines vorkonfigurierten `HttpClient` Diensts aufruft.</span><span class="sxs-lookup"><span data-stu-id="7d5da-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="7d5da-106">Verfassen von Anforderungen, die JavaScript-Abruf- [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Optionen enthalten können, unter Verwendung Blazor JSON-Hilfsprogramme oder mit <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="7d5da-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="7d5da-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) -apps Web-APIs mithilfe <xref:System.Net.Http.HttpClient> Instanzen, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory>erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="7d5da-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="7d5da-108">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="7d5da-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="7d5da-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([herunterladen](xref:index#how-to-download-a-sample)) &ndash; wählen Sie die APP *blazorwebassemblysample* aus.</span><span class="sxs-lookup"><span data-stu-id="7d5da-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="7d5da-110">Weitere Informationen finden Sie in der Beispiel-app " *blazorwebassemblysample* " in den folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="7d5da-110">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="7d5da-111">Aufrufen der Web-API (*pages/callwebapi. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7d5da-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="7d5da-112">HTTP-Anforderungs Tester (*Components/httprequesttester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="7d5da-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="7d5da-113">Pakete</span><span class="sxs-lookup"><span data-stu-id="7d5da-113">Packages</span></span>

<span data-ttu-id="7d5da-114">Verweisen Sie auf die *experimentelle* [Microsoft. aspnetcore.Blazor. HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) -nuget-Paket in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="7d5da-114">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="7d5da-115">`Microsoft.AspNetCore.Blazor.HttpClient` basiert auf `HttpClient` und [System. Text. JSON](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="7d5da-115">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="7d5da-116">Verwenden Sie zum Verwenden einer stabilen API das [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) -Paket, das " [newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)verwendet.</span><span class="sxs-lookup"><span data-stu-id="7d5da-116">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="7d5da-117">Die Verwendung der stabilen API in `Microsoft.AspNet.WebApi.Client` bietet keine der in diesem Thema beschriebenen JSON-Hilfsprogramme, die für das experimentelle `Microsoft.AspNetCore.Blazor.HttpClient` Paket eindeutig sind.</span><span class="sxs-lookup"><span data-stu-id="7d5da-117">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="7d5da-118">HttpClient-und JSON-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="7d5da-118">HttpClient and JSON helpers</span></span>

<span data-ttu-id="7d5da-119">In einer Blazor Webassembly-App steht [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst zur Verfügung, um Anforderungen an den Ursprungsserver zurückzusenden.</span><span class="sxs-lookup"><span data-stu-id="7d5da-119">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="7d5da-120">Eine Blazor Server-app enthält standardmäßig keinen `HttpClient` Dienst.</span><span class="sxs-lookup"><span data-stu-id="7d5da-120">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="7d5da-121">Stellen Sie mithilfe der [HttpClient-Factory-Infrastruktur](xref:fundamentals/http-requests)eine `HttpClient` für die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="7d5da-121">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="7d5da-122">`HttpClient`-und JSON-Hilfsprogramme werden auch verwendet, um Web-API-Endpunkte von Drittanbietern aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="7d5da-122">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="7d5da-123">`HttpClient` wird mithilfe der Browser- [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) implementiert und unterliegt seinen Beschränkungen, einschließlich der Erzwingung derselben Ursprungs Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="7d5da-123">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="7d5da-124">Die Basisadresse des Clients wird auf die Adresse des Ursprungs Servers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7d5da-124">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="7d5da-125">Fügen Sie mit der `@inject`-Direktive eine `HttpClient`-Instanz ein:</span><span class="sxs-lookup"><span data-stu-id="7d5da-125">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="7d5da-126">In den folgenden Beispielen verarbeitet eine TODO-Web-API Vorgänge zum Erstellen, lesen, aktualisieren und löschen (CRUD).</span><span class="sxs-lookup"><span data-stu-id="7d5da-126">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="7d5da-127">Die Beispiele basieren auf einer `TodoItem`-Klasse, die die speichert:</span><span class="sxs-lookup"><span data-stu-id="7d5da-127">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="7d5da-128">ID (`Id``long`) &ndash; eindeutige ID des Elements.</span><span class="sxs-lookup"><span data-stu-id="7d5da-128">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="7d5da-129">Name (`Name``string`) &ndash; Name des Elements.</span><span class="sxs-lookup"><span data-stu-id="7d5da-129">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="7d5da-130">Status (`IsComplete``bool`) &ndash; Angabe, ob das TODO-Element abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="7d5da-130">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="7d5da-131">JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:</span><span class="sxs-lookup"><span data-stu-id="7d5da-131">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="7d5da-132">`GetJsonAsync` &ndash; sendet eine HTTP GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7d5da-132">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7d5da-133">Im folgenden Code werden die `_todoItems` von der Komponente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7d5da-133">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="7d5da-134">Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendering der Komponente abgeschlossen ist ([oninitializedasync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="7d5da-134">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="7d5da-135">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="7d5da-135">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="7d5da-136">`PostJsonAsync` &ndash; sendet eine HTTP POST-Anforderung, einschließlich JSON-codiertem Inhalt, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7d5da-136">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7d5da-137">Im folgenden Code wird `_newItemName` von einem gebundenen Element der-Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7d5da-137">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="7d5da-138">Die `AddItem`-Methode wird durch Auswahl eines `<button>` Elements ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7d5da-138">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="7d5da-139">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="7d5da-139">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="7d5da-140">`PutJsonAsync` &ndash; sendet eine HTTP PUT-Anforderung, einschließlich JSON-codiertem Inhalt.</span><span class="sxs-lookup"><span data-stu-id="7d5da-140">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="7d5da-141">Im folgenden Code werden `_editItem` Werte für `Name` und `IsCompleted` von gebundenen Elementen der Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7d5da-141">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="7d5da-142">Die `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="7d5da-142">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="7d5da-143">Die `SaveItem`-Methode wird ausgelöst, indem Sie das Save `<button>`-Element auswählen.</span><span class="sxs-lookup"><span data-stu-id="7d5da-143">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="7d5da-144">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="7d5da-144">See the sample app for a complete example.</span></span>

  ```razor
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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="7d5da-145"><xref:System.Net.Http> enthält zusätzliche Erweiterungs Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="7d5da-145"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="7d5da-146">" [HttpClient. deleteasync](xref:System.Net.Http.HttpClient.DeleteAsync*) " wird verwendet, um eine HTTP DELETE-Anforderung an eine Web-API zu senden.</span><span class="sxs-lookup"><span data-stu-id="7d5da-146">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="7d5da-147">Im folgenden Code Ruft das DELETE `<button>`-Element die `DeleteItem`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="7d5da-147">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="7d5da-148">Das gebundene `<input>` Element stellt die `id` des zu löschenden Elements bereit.</span><span class="sxs-lookup"><span data-stu-id="7d5da-148">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="7d5da-149">Ein umfassendes Beispiel finden Sie in der Beispiel-app.</span><span class="sxs-lookup"><span data-stu-id="7d5da-149">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="7d5da-150">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)</span><span class="sxs-lookup"><span data-stu-id="7d5da-150">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="7d5da-151">Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bedient hat.</span><span class="sxs-lookup"><span data-stu-id="7d5da-151">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="7d5da-152">Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7d5da-152">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7d5da-153">Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest.</span><span class="sxs-lookup"><span data-stu-id="7d5da-153">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7d5da-154">Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu senden, muss der *Endpunkt* [Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/)aktivieren.</span><span class="sxs-lookup"><span data-stu-id="7d5da-154">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="7d5da-155">Die [Blazor Webassembly-Beispiel-app (blazorwebassemblysample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) veranschaulicht die Verwendung von cors in der Web-API-Aufruf Komponente (*pages/callwebapi. Razor*).</span><span class="sxs-lookup"><span data-stu-id="7d5da-155">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="7d5da-156">Informationen dazu, wie Sie anderen Standorten cors-Anforderungen (Cross-Origin Resource Sharing) an Ihre APP senden können, finden Sie unter <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="7d5da-156">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="7d5da-157">HttpClient und httprequestmessage mit Abruf-API-Anforderungs Optionen</span><span class="sxs-lookup"><span data-stu-id="7d5da-157">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="7d5da-158">Verwenden Sie zum Anpassen von Anforderungen bei der Ausführung in Webassembly in einer Blazor Webassembly-APP [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="7d5da-158">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="7d5da-159">Sie können z. b. den Anforderungs-URI, die HTTP-Methode und alle gewünschten Anforderungs Header angeben.</span><span class="sxs-lookup"><span data-stu-id="7d5da-159">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
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
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="7d5da-160">Weitere Informationen zu den Abruf-API-Optionen finden Sie unter [MDN-Webdokumentation: windoworworkerglobalscope. FETCH ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="7d5da-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="7d5da-161">Beim Senden von Anmelde Informationen (Autorisierungs Cookies/-Header) für cors-Anforderungen muss der `Authorization`-Header von der cors-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="7d5da-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="7d5da-162">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="7d5da-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="7d5da-163">Anforderungs Ursprünge (`http://localhost:5000``https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="7d5da-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="7d5da-164">Beliebige Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="7d5da-164">Any method (verb).</span></span>
* <span data-ttu-id="7d5da-165">`Content-Type` und `Authorization` Header.</span><span class="sxs-lookup"><span data-stu-id="7d5da-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="7d5da-166">Um einen benutzerdefinierten Header (z. b. `x-custom-header`) zuzulassen, Listen Sie den-Header auf, wenn Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7d5da-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="7d5da-167">Anmelde Informationen, die durch Client seitigen JavaScript-Code festgelegt wurden (`credentials`-Eigenschaft auf `include`festgelegt).</span><span class="sxs-lookup"><span data-stu-id="7d5da-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="7d5da-168">Weitere Informationen finden Sie unter <xref:security/cors> und der Komponente "http-Anforderungs Tester" der Beispiel-app (*Components/httprequesttester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="7d5da-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d5da-169">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7d5da-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="7d5da-170">Konfiguration des Kestrel-HTTPS-Endpunkts</span><span class="sxs-lookup"><span data-stu-id="7d5da-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="7d5da-171">Cross-Origin Resource Sharing (cors) bei W3C</span><span class="sxs-lookup"><span data-stu-id="7d5da-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
