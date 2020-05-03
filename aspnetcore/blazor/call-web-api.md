---
title: Aufrufen einer Web-API über ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie mithilfe von JSON-Hilfsprogramme eine Web-API aus einer Blazor WebAssembly-App aufrufen können, einschließlich der Erstellung von CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122218"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="969e5-103">Aufrufen einer Web-API über ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="969e5-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="969e5-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="969e5-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="969e5-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)-Apps rufen Web-APIs über einen vorkonfigurierten `HttpClient`-Dienst auf.</span><span class="sxs-lookup"><span data-stu-id="969e5-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="969e5-106">Erstellen Sie Anforderungen, die JavaScript [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)-Optionen enthalten können, mit Blazor JSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="969e5-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="969e5-107">Der `HttpClient`-Dienst in Blazor WebAssembly-Apps konzentriert sich darauf, Anforderungen an den Ursprungsserver zurückzusenden.</span><span class="sxs-lookup"><span data-stu-id="969e5-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="969e5-108">Die Anleitung in diesem Thema bezieht sich nur auf Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="969e5-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="969e5-109">[Blazor Server](xref:blazor/hosting-models#blazor-server)-Apps rufen Web-APIs über <xref:System.Net.Http.HttpClient>-Instanzen auf, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory> erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="969e5-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="969e5-110">Die Anleitung in diesem Thema bezieht sich nicht auf Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="969e5-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="969e5-111">Befolgen Sie bei der Entwicklung von Blazor Server-Apps die Anleitung in <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="969e5-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="969e5-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) &ndash; Wählen Sie die *BlazorWebAssemblySample*-App aus.</span><span class="sxs-lookup"><span data-stu-id="969e5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="969e5-113">Weitere Informationen finden Sie in der *BlazorWebAssemblySample*-Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="969e5-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="969e5-114">Aufrufen der Web-API (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="969e5-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="969e5-115">HTTP-Anforderungstester (*Components/httprequesttester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="969e5-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="969e5-116">Pakete</span><span class="sxs-lookup"><span data-stu-id="969e5-116">Packages</span></span>

<span data-ttu-id="969e5-117">Verweisen Sie in der Projektdatei auf das NuGet-Paket [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/).</span><span class="sxs-lookup"><span data-stu-id="969e5-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="969e5-118">Hinzufügen des HttpClient-Diensts</span><span class="sxs-lookup"><span data-stu-id="969e5-118">Add the HttpClient service</span></span>

<span data-ttu-id="969e5-119">Fügen Sie in `Program.Main` einen `HttpClient`-Dienst hinzu, wenn dieser noch nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="969e5-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="969e5-120">HttpClient- und JSON-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="969e5-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="969e5-121">In einer Blazor WebAssembly-App ist [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst für die Rückübertragung von Anforderungen an den Ursprungsserver verfügbar.</span><span class="sxs-lookup"><span data-stu-id="969e5-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="969e5-122">Eine Blazor Server-App enthält standardmäßig keinen `HttpClient`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="969e5-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="969e5-123">Stellen Sie der App über die [HttpClient-Factoryinfrastruktur](xref:fundamentals/http-requests) einen `HttpClient` zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="969e5-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="969e5-124">`HttpClient`- und JSON-Hilfsprogramme werden auch zum Aufrufen von Web-API-Endpunkten von Drittanbietern verwendet.</span><span class="sxs-lookup"><span data-stu-id="969e5-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="969e5-125">`HttpClient` wird mithilfe der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) des Browsers implementiert und unterliegt dessen Beschränkungen, einschließlich der Durchsetzung derselben Ursprungsrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="969e5-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="969e5-126">Die Basisadresse des Clients wird auf die Adresse des Ursprungsservers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="969e5-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="969e5-127">Fügen Sie eine `HttpClient`-Instanz mit der `@inject`-Direktive ein:</span><span class="sxs-lookup"><span data-stu-id="969e5-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="969e5-128">In den folgenden Beispielen erstellt, liest, aktualisiert und löscht eine Todo-Web-API Vorgänge (CRUD).</span><span class="sxs-lookup"><span data-stu-id="969e5-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="969e5-129">Die Beispiele basieren auf einer `TodoItem`-Klasse, die Folgendes speichert:</span><span class="sxs-lookup"><span data-stu-id="969e5-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="969e5-130">ID (`Id`, `long`) &ndash; Eindeutige ID des Elements.</span><span class="sxs-lookup"><span data-stu-id="969e5-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="969e5-131">Name (`Name`, `string`) &ndash; Name des Elements.</span><span class="sxs-lookup"><span data-stu-id="969e5-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="969e5-132">Status (`IsComplete`, `bool`) &ndash; Angabe, ob das Todo-Element abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="969e5-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="969e5-133">JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:</span><span class="sxs-lookup"><span data-stu-id="969e5-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="969e5-134">`GetFromJsonAsync` &ndash; Sendet eine HTTP-GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="969e5-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="969e5-135">Im folgenden Code werden die `_todoItems` von der Komponente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="969e5-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="969e5-136">Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendern der Komponente abgeschlossen ist ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="969e5-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="969e5-137">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="969e5-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="969e5-138">`PostAsJsonAsync` &ndash; Sendet eine HTTP-POST-Anforderung, einschließlich JSON-codierten Inhalten, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="969e5-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="969e5-139">Im folgenden Code wird `_newItemName` durch ein gebundenes Element der Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="969e5-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="969e5-140">Die `AddItem`-Methode wird durch Auswählen eines `<button>`-Elements ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="969e5-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="969e5-141">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="969e5-141">See the sample app for a complete example.</span></span>

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
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="969e5-142">Aufrufe von `PostAsJsonAsync` geben eine <xref:System.Net.Http.HttpResponseMessage> zurück.</span><span class="sxs-lookup"><span data-stu-id="969e5-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="969e5-143">Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die `ReadFromJsonAsync<T>`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="969e5-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="969e5-144">`PutAsJsonAsync` &ndash; Sendet eine HTTP-PUT-Anforderung, einschließlich JSON-codierter Inhalte.</span><span class="sxs-lookup"><span data-stu-id="969e5-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="969e5-145">Im folgenden Code werden `_editItem`-Werte für `Name` und `IsCompleted` durch gebundene Elemente der Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="969e5-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="969e5-146">Die `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="969e5-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="969e5-147">Die Methode `SaveItem` wird durch Auswahl des `<button>`-Elements „Speichern“ ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="969e5-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="969e5-148">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="969e5-148">See the sample app for a complete example.</span></span>

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
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="969e5-149">Aufrufe von `PutAsJsonAsync` geben eine <xref:System.Net.Http.HttpResponseMessage> zurück.</span><span class="sxs-lookup"><span data-stu-id="969e5-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="969e5-150">Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die `ReadFromJsonAsync<T>`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="969e5-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="969e5-151"><xref:System.Net.Http> enthält zusätzliche Erweiterungsmethoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="969e5-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="969e5-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) wird verwendet, um eine HTTP-DELETE-Anforderung an eine Web-API zu senden.</span><span class="sxs-lookup"><span data-stu-id="969e5-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="969e5-153">Im folgenden Code ruft das `<button>`-Element „Löschen“ die `DeleteItem`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="969e5-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="969e5-154">Das gebundene `<input>`-Element stellt die `id` des zu löschenden Elements bereit.</span><span class="sxs-lookup"><span data-stu-id="969e5-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="969e5-155">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="969e5-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="969e5-156">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)</span><span class="sxs-lookup"><span data-stu-id="969e5-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="969e5-157">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="969e5-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="969e5-158">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="969e5-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="969e5-159">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="969e5-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="969e5-160">Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu stellen, muss der *Endpunkt* die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="969e5-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="969e5-161">Die [Blazor WebAssembly-Beispiel-App (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) veranschaulicht die Verwendung von CORS in der Komponente „Web-API aufrufen“ (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="969e5-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="969e5-162">Informationen darüber, wie Sie anderen Websites ermöglichen, CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen) an Ihre App zu stellen, finden Sie unter <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="969e5-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="969e5-163">HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="969e5-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="969e5-164">Wenn Sie WebAssembly in einer Blazor WebAssembly-App ausführen, verwenden Sie [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> zum Anpassen von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="969e5-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="969e5-165">Sie können z. B. den Anforderungs-URI, die HTTP-Methode und beliebige Anforderungsheader angeben.</span><span class="sxs-lookup"><span data-stu-id="969e5-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="969e5-166">Die .NET-WebAssembly-Implementierung von `HttpClient` verwendet [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="969e5-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="969e5-167">FETCH ermöglicht die Konfiguration mehrerer [anforderungsspezifischer Optionen](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="969e5-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="969e5-168">HTTP FETCH-Anforderungsoptionen können mit `HttpRequestMessage`-Erweiterungsmethoden konfiguriert werden, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="969e5-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="969e5-169">`HttpRequestMessage`-Erweiterungsmethode</span><span class="sxs-lookup"><span data-stu-id="969e5-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="969e5-170">FETCH-Anforderungseigenschaft</span><span class="sxs-lookup"><span data-stu-id="969e5-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="969e5-171">credentials</span><span class="sxs-lookup"><span data-stu-id="969e5-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="969e5-172">cache</span><span class="sxs-lookup"><span data-stu-id="969e5-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="969e5-173">mode</span><span class="sxs-lookup"><span data-stu-id="969e5-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="969e5-174">integrity</span><span class="sxs-lookup"><span data-stu-id="969e5-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="969e5-175">Sie können weitere Optionen mithilfe der allgemeineren `SetBrowserRequestOption`-Erweiterungsmethode festlegen.</span><span class="sxs-lookup"><span data-stu-id="969e5-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="969e5-176">Die HTTP-Antwort wird in der Regel in einer Blazor-WebAssembly-App gepuffert, um Unterstützung für Synchronisierungslesevorgänge im Antwortinhalt zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="969e5-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="969e5-177">Um Unterstützung für Streaming von Antworten zu aktivieren, verwenden Sie die `SetBrowserResponseStreamingEnabled`-Erweiterungsmethode für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="969e5-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="969e5-178">Wenn Sie Anmeldeinformationen in eine ursprungsübergreifende Anforderung einschließen möchten, verwenden Sie die `SetBrowserRequestCredentials`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="969e5-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="969e5-179">Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="969e5-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="969e5-180">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="969e5-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="969e5-181">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="969e5-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="969e5-182">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="969e5-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="969e5-183">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="969e5-183">Any method (verb).</span></span>
* <span data-ttu-id="969e5-184">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="969e5-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="969e5-185">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="969e5-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="969e5-186">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="969e5-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="969e5-187">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="969e5-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="969e5-188">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="969e5-188">Additional resources</span></span>

* [<span data-ttu-id="969e5-189">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="969e5-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="969e5-190">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="969e5-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="969e5-191">Kestrel HTTPS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="969e5-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="969e5-192">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) beim W3C</span><span class="sxs-lookup"><span data-stu-id="969e5-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
