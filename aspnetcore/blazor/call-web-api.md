---
title: Aufrufen einer Web-API über ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie mithilfe von JSON-Hilfsprogramme eine Web-API aus einer Blazor WebAssembly-App aufrufen können, einschließlich der Erstellung von CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: db1f6a357f63b405bf2f3b98e51c9aeffda97d66
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242523"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="f269d-103">Aufrufen einer Web-API über ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f269d-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="f269d-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="f269d-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="f269d-105">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="f269d-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="f269d-106">[Blazor Server](xref:blazor/hosting-models#blazor-server)-Apps rufen Web-APIs über <xref:System.Net.Http.HttpClient>-Instanzen auf, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory> erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="f269d-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="f269d-107">Anleitungen für Blazor Server finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="f269d-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="f269d-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)-Apps rufen Web-APIs über einen vorkonfigurierten <xref:System.Net.Http.HttpClient>-Dienst auf.</span><span class="sxs-lookup"><span data-stu-id="f269d-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="f269d-109">Erstellen Sie Anforderungen, die JavaScript [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)-Optionen enthalten können, mit BlazorJSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="f269d-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="f269d-110">Der <xref:System.Net.Http.HttpClient>-Dienst in Blazor WebAssembly-Apps konzentriert sich darauf, Anforderungen an den Ursprungsserver zurückzusenden.</span><span class="sxs-lookup"><span data-stu-id="f269d-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="f269d-111">Die Anleitung in diesem Thema bezieht sich nur auf Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="f269d-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="f269d-112">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)): Wählen Sie die `BlazorWebAssemblySample`-App aus.</span><span class="sxs-lookup"><span data-stu-id="f269d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="f269d-113">Sehen Sie sich die folgenden Komponenten in der `BlazorWebAssemblySample`-Beispiel-App an:</span><span class="sxs-lookup"><span data-stu-id="f269d-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="f269d-114">Aufrufen der Web-API (`Pages/CallWebAPI.razor`)</span><span class="sxs-lookup"><span data-stu-id="f269d-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="f269d-115">HTTP-Anforderungstester (`Components/HTTPRequestTester.razor`)</span><span class="sxs-lookup"><span data-stu-id="f269d-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="f269d-116">Pakete</span><span class="sxs-lookup"><span data-stu-id="f269d-116">Packages</span></span>

<span data-ttu-id="f269d-117">Verweisen Sie in der Projektdatei auf das NuGet-Paket [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/).</span><span class="sxs-lookup"><span data-stu-id="f269d-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="f269d-118">Hinzufügen des HttpClient-Diensts</span><span class="sxs-lookup"><span data-stu-id="f269d-118">Add the HttpClient service</span></span>

<span data-ttu-id="f269d-119">Fügen Sie in `Program.Main` einen <xref:System.Net.Http.HttpClient>-Dienst hinzu, wenn dieser noch nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="f269d-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="f269d-120">HttpClient- und JSON-Hilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="f269d-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="f269d-121">In einer Blazor WebAssembly-App ist [`HttpClient`](xref:fundamentals/http-requests) als vorkonfigurierter Dienst für die Rückübertragung von Anforderungen an den Ursprungsserver verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f269d-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="f269d-122">Eine Blazor Server-App enthält standardmäßig keinen <xref:System.Net.Http.HttpClient>-Dienst.</span><span class="sxs-lookup"><span data-stu-id="f269d-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="f269d-123">Stellen Sie der App über die [`HttpClient`-Factory-Infrastruktur](xref:fundamentals/http-requests) einen <xref:System.Net.Http.HttpClient> zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f269d-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="f269d-124"><xref:System.Net.Http.HttpClient>- und JSON-Hilfsprogramme werden auch zum Aufrufen von Web-API-Endpunkten von Drittanbietern verwendet.</span><span class="sxs-lookup"><span data-stu-id="f269d-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="f269d-125"><xref:System.Net.Http.HttpClient> wird mithilfe der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) des Browsers implementiert und unterliegt dessen Beschränkungen, einschließlich der Durchsetzung derselben Ursprungsrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="f269d-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="f269d-126">Die Basisadresse des Clients wird auf die Adresse des Ursprungsservers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f269d-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="f269d-127">Fügen Sie eine <xref:System.Net.Http.HttpClient>-Instanz mit der [`@inject`](xref:mvc/views/razor#inject)-Direktive ein:</span><span class="sxs-lookup"><span data-stu-id="f269d-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="f269d-128">In den folgenden Beispielen erstellt, liest, aktualisiert und löscht eine Todo-Web-API Vorgänge (CRUD).</span><span class="sxs-lookup"><span data-stu-id="f269d-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="f269d-129">Die Beispiele basieren auf einer `TodoItem`-Klasse, die Folgendes speichert:</span><span class="sxs-lookup"><span data-stu-id="f269d-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="f269d-130">ID (`Id`, `long`): Eindeutige ID des Elements.</span><span class="sxs-lookup"><span data-stu-id="f269d-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="f269d-131">Name (`Name`, `string`): Der Name des Elements.</span><span class="sxs-lookup"><span data-stu-id="f269d-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="f269d-132">Status (`IsComplete`, `bool`): Angabe, ob das Todo-Element abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="f269d-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="f269d-133">JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:</span><span class="sxs-lookup"><span data-stu-id="f269d-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="f269d-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sendet eine HTTP-GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f269d-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="f269d-135">Im folgenden Code werden die `todoItems` von der Komponente angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f269d-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="f269d-136">Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendern der Komponente abgeschlossen ist ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="f269d-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="f269d-137">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="f269d-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="f269d-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sendet eine HTTP-POST-Anforderung, einschließlich JSON-codierten Inhalten, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f269d-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="f269d-139">Im folgenden Code wird `newItemName` durch ein gebundenes Element der Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f269d-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="f269d-140">Die `AddItem`-Methode wird durch Auswählen eines `<button>`-Elements ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f269d-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="f269d-141">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="f269d-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="f269d-142">Aufrufe von <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> geben eine <xref:System.Net.Http.HttpResponseMessage> zurück.</span><span class="sxs-lookup"><span data-stu-id="f269d-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f269d-143">Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die `ReadFromJsonAsync<T>`-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="f269d-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="f269d-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sendet eine HTTP-PUT-Anforderung, einschließlich JSON-codierter Inhalte.</span><span class="sxs-lookup"><span data-stu-id="f269d-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="f269d-145">Im folgenden Code werden `editItem`-Werte für `Name` und `IsCompleted` durch gebundene Elemente der Komponente bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f269d-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="f269d-146">Die `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="f269d-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="f269d-147">Die Methode `SaveItem` wird durch Auswahl des `<button>`-Elements „Speichern“ ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f269d-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="f269d-148">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="f269d-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="f269d-149">Aufrufe von <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> geben eine <xref:System.Net.Http.HttpResponseMessage> zurück.</span><span class="sxs-lookup"><span data-stu-id="f269d-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="f269d-150">Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>-Erweiterungsmethode:</span><span class="sxs-lookup"><span data-stu-id="f269d-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="f269d-151"><xref:System.Net.Http> enthält zusätzliche Erweiterungsmethoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="f269d-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="f269d-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> wird verwendet, um eine HTTP-DELETE-Anforderung an eine Web-API zu senden.</span><span class="sxs-lookup"><span data-stu-id="f269d-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="f269d-153">Im folgenden Code ruft das `<button>`-Element „Löschen“ die `DeleteItem`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="f269d-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="f269d-154">Das gebundene `<input>`-Element stellt die `id` des zu löschenden Elements bereit.</span><span class="sxs-lookup"><span data-stu-id="f269d-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="f269d-155">Ein vollständiges Beispiel finden Sie in der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="f269d-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="f269d-156">Benannte HttpClient-Instanz mit IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="f269d-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="f269d-157"><xref:System.Net.Http.IHttpClientFactory>-Dienste und die Konfiguration einer benannten <xref:System.Net.Http.HttpClient> werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f269d-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="f269d-158">Verweisen Sie in der Projektdatei auf das NuGet-Paket [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="f269d-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package in the project file.</span></span>

<span data-ttu-id="f269d-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f269d-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="f269d-160">`FetchData`-Komponente (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="f269d-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="f269d-161">Typisierte HttpClient-Instanz</span><span class="sxs-lookup"><span data-stu-id="f269d-161">Typed HttpClient</span></span>

<span data-ttu-id="f269d-162">Eine typisierte <xref:System.Net.Http.HttpClient> verwendet mindestens eine <xref:System.Net.Http.HttpClient>-Instanz der App (Standardinstanz oder benannte Instanz), um Daten aus einem oder mehreren Web-API-Endpunkten zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="f269d-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="f269d-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="f269d-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="f269d-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="f269d-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="f269d-165">Komponenten fügen die typisierte <xref:System.Net.Http.HttpClient>-Instanz zum Aufruf der Web-API ein.</span><span class="sxs-lookup"><span data-stu-id="f269d-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="f269d-166">`FetchData`-Komponente (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="f269d-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="f269d-167">Behandeln von Fehlern</span><span class="sxs-lookup"><span data-stu-id="f269d-167">Handle errors</span></span>

<span data-ttu-id="f269d-168">Wenn Fehler beim Interagieren mit einer Web-API auftreten, können sie durch den Entwicklercode behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="f269d-168">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="f269d-169">Beispielsweise erwartet <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> eine JSON-Antwort von der Server-API mit einem `Content-Type` von `application/json`.</span><span class="sxs-lookup"><span data-stu-id="f269d-169">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="f269d-170">Wenn die Antwort nicht im JSON-Format vorliegt, löst die Inhaltsüberprüfung eine <xref:System.NotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="f269d-170">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="f269d-171">Im folgenden Beispiel ist der URI-Endpunkt für die Anforderung der Wettervorhersagedaten falsch geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f269d-171">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="f269d-172">Der URI sollte `WeatherForecast` sein, aber wird im Befehl als `WeatherForcast` (fehlendes „e“) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f269d-172">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="f269d-173">Der <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>-Aufruf erwartet, dass JSON zurückgegeben wird, der Server gibt jedoch HTML für einen Ausnahmefehler auf dem Server mit einem `Content-Type` von `text/html` zurück.</span><span class="sxs-lookup"><span data-stu-id="f269d-173">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="f269d-174">Der Ausnahmefehler tritt auf dem Server auf, da der Pfad nicht gefunden wurde und die Middleware keine Seite oder Ansicht für die Anforderung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="f269d-174">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="f269d-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> auf dem Client wird <xref:System.NotSupportedException> ausgelöst, wenn der Inhalt der Antwort als Nicht-JSON-Code überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="f269d-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="f269d-176">Die Ausnahme wird im `catch`-Block abgefangen, in dem die benutzerdefinierte Logik den Fehler protokollieren oder eine benutzerfreundliche Fehlermeldung an den Benutzer senden kann.</span><span class="sxs-lookup"><span data-stu-id="f269d-176">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="f269d-177">Das vorherige Beispiel dient nur der Veranschaulichung.</span><span class="sxs-lookup"><span data-stu-id="f269d-177">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="f269d-178">Eine Web-API-Server-App kann so konfiguriert werden, dass JSON auch dann zurückgegeben wird, wenn kein Endpunkt vorhanden ist oder ein Ausnahmefehler auf dem Server auftritt.</span><span class="sxs-lookup"><span data-stu-id="f269d-178">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="f269d-179">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="f269d-179">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="f269d-180">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)</span><span class="sxs-lookup"><span data-stu-id="f269d-180">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="f269d-181">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="f269d-181">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="f269d-182">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="f269d-182">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="f269d-183">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="f269d-183">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="f269d-184">Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu stellen, muss der *Endpunkt* die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/) ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="f269d-184">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="f269d-185">Die [Blazor WebAssembly-Beispiel-App (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) veranschaulicht die Verwendung von CORS in der Komponente „Web-API aufrufen“ (`Pages/CallWebAPI.razor`).</span><span class="sxs-lookup"><span data-stu-id="f269d-185">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="f269d-186">Informationen darüber, wie Sie anderen Websites ermöglichen, CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen) an Ihre App zu stellen, finden Sie unter <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="f269d-186">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f269d-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f269d-187">Additional resources</span></span>

* <span data-ttu-id="f269d-188"><xref:blazor/security/webassembly/additional-scenarios>: Umfasst Informationen zur Verwendung von <xref:System.Net.Http.HttpClient>, um sichere Web-API-Anforderungen auszugeben.</span><span class="sxs-lookup"><span data-stu-id="f269d-188"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="f269d-189">Kestrel HTTPS-Endpunktkonfiguration</span><span class="sxs-lookup"><span data-stu-id="f269d-189">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="f269d-190">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) beim W3C</span><span class="sxs-lookup"><span data-stu-id="f269d-190">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
