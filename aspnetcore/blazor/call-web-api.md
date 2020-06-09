---
title: "title: 'Aufrufen einer Web-API über ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Erfahren Sie, wie Sie mithilfe von JSON-Hilfsprogramme eine Web-API aus einer Blazor WebAssembly-App aufrufen können, einschließlich der Erstellung von CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen).'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
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
ms.openlocfilehash: 4f3fd1fca075098a94756757474b2cad3cc99775
ms.sourcegitcommit: d8c35c37c90f81bd2cd7ae5e1700f2fede1b4b75
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84214780"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: blazor/call-web-api Aufrufen einer Web-API über ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Juan De la Cruz](https://github.com/juandelacruz23) [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)-Apps rufen Web-APIs über einen vorkonfigurierten <xref:System.Net.Http.HttpClient>-Dienst auf. Erstellen Sie Anforderungen, die JavaScript [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)-Optionen enthalten können, mit BlazorJSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage>.

Der <xref:System.Net.Http.HttpClient>-Dienst in Blazor WebAssembly-Apps konzentriert sich darauf, Anforderungen an den Ursprungsserver zurückzusenden.

Die Anleitung in diesem Thema bezieht sich nur auf Blazor WebAssembly-Apps.

* [Blazor Server](xref:blazor/hosting-models#blazor-server)-Apps rufen Web-APIs über <xref:System.Net.Http.HttpClient>-Instanzen auf, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory> erstellt werden.
* Die Anleitung in diesem Thema bezieht sich nicht auf Blazor Server-Apps.

## <a name="packages"></a>Befolgen Sie bei der Entwicklung von Blazor Server-Apps die Anleitung in <xref:fundamentals/http-requests>.

[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)): Wählen Sie die *BlazorWebAssemblySample*-App aus.

## <a name="add-the-httpclient-service"></a>Weitere Informationen finden Sie in der *BlazorWebAssemblySample*-Beispiel-App:

Aufrufen der Web-API (*Pages/CallWebAPI.razor*)

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HTTP-Anforderungstester (*Components/httprequesttester. Razor*)

Pakete

Verweisen Sie in der Projektdatei auf das NuGet-Paket [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/). Hinzufügen des HttpClient-Diensts

Fügen Sie in `Program.Main` einen <xref:System.Net.Http.HttpClient>-Dienst hinzu, wenn dieser noch nicht vorhanden ist: HttpClient- und JSON-Hilfsprogramme

In einer Blazor WebAssembly-App ist [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst für die Rückübertragung von Anforderungen an den Ursprungsserver verfügbar. Eine Blazor Server-App enthält standardmäßig keinen <xref:System.Net.Http.HttpClient>-Dienst.

```razor
@using System.Net.Http
@inject HttpClient Http
```

Stellen Sie der App über die [HttpClient-Factoryinfrastruktur](xref:fundamentals/http-requests) einen <xref:System.Net.Http.HttpClient> zur Verfügung. <xref:System.Net.Http.HttpClient>- und JSON-Hilfsprogramme werden auch zum Aufrufen von Web-API-Endpunkten von Drittanbietern verwendet.

* <xref:System.Net.Http.HttpClient> wird mithilfe der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) des Browsers implementiert und unterliegt dessen Beschränkungen, einschließlich der Durchsetzung derselben Ursprungsrichtlinie.
* Die Basisadresse des Clients wird auf die Adresse des Ursprungsservers festgelegt.
* Fügen Sie eine <xref:System.Net.Http.HttpClient>-Instanz mit der [`@inject`](xref:mvc/views/razor#inject)-Direktive ein:

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

In den folgenden Beispielen erstellt, liest, aktualisiert und löscht eine Todo-Web-API Vorgänge (CRUD).

* Die Beispiele basieren auf einer `TodoItem`-Klasse, die Folgendes speichert:

  ID (`Id`, `long`): Eindeutige ID des Elements. Name (`Name`, `string`): Der Name des Elements. Status (`IsComplete`, `bool`): Angabe, ob das Todo-Element abgeschlossen wurde.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:

  <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sendet eine HTTP-GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen. Im folgenden Code werden die `todoItems` von der Komponente angezeigt. Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendern der Komponente abgeschlossen ist ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).

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
  
  Ein vollständiges Beispiel finden Sie in der Beispiel-App. <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sendet eine HTTP-POST-Anforderung, einschließlich JSON-codierten Inhalten, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* Im folgenden Code wird `newItemName` durch ein gebundenes Element der Komponente bereitgestellt.

  Die `AddItem`-Methode wird durch Auswählen eines `<button>`-Elements ausgelöst. Ein vollständiges Beispiel finden Sie in der Beispiel-App. Aufrufe von <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> geben eine <xref:System.Net.Http.HttpResponseMessage> zurück. Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die `ReadFromJsonAsync<T>`-Erweiterungsmethode:

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
  
  <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sendet eine HTTP-PUT-Anforderung, einschließlich JSON-codierter Inhalte. Im folgenden Code werden `editItem`-Werte für `Name` und `IsCompleted` durch gebundene Elemente der Komponente bereitgestellt.
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

Die `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird. Die Methode `SaveItem` wird durch Auswahl des `<button>`-Elements „Speichern“ ausgelöst.

Ein vollständiges Beispiel finden Sie in der Beispiel-App. Aufrufe von <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> geben eine <xref:System.Net.Http.HttpResponseMessage> zurück. Um den JSON-Inhalt aus der Antwortnachricht zu deserialisieren, verwenden Sie die <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A>-Erweiterungsmethode:

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><xref:System.Net.Http> enthält zusätzliche Erweiterungsmethoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten.

<xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> wird verwendet, um eine HTTP-DELETE-Anforderung an eine Web-API zu senden.

Im folgenden Code ruft das `<button>`-Element „Löschen“ die `DeleteItem`-Methode auf.

Das gebundene `<input>`-Element stellt die `id` des zu löschenden Elements bereit.

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Ein vollständiges Beispiel finden Sie in der Beispiel-App.

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

## <a name="typed-httpclient"></a>Benannte HttpClient-Instanz mit IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory>-Dienste und die Konfiguration einer benannten <xref:System.Net.Http.HttpClient> werden unterstützt.

Verweisen Sie auf das NuGet-Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) in der Projektdatei.

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

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`-Komponente (*Pages/FetchData.razor*):

Typisierte HttpClient-Instanz

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

## <a name="handle-errors"></a>Eine typisierte <xref:System.Net.Http.HttpClient> verwendet mindestens eine <xref:System.Net.Http.HttpClient>-Instanz der App (Standardinstanz oder benannte Instanz), um Daten aus einem oder mehreren Web-API-Endpunkten zurückzugeben.

*WeatherForecastClient.cs*: `Program.Main` (*Program.cs*): Komponenten fügen die typisierte <xref:System.Net.Http.HttpClient>-Instanz zum Aufruf der Web-API ein.

`FetchData`-Komponente (*Pages/FetchData.razor*): Behandeln von Fehlern

Wenn Fehler beim Interagieren mit einer Web-API auftreten, können sie durch den Entwicklercode behandelt werden. Beispielsweise erwartet <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> eine JSON-Antwort von der Server-API mit einem `Content-Type` von `application/json`.

Wenn die Antwort nicht im JSON-Format vorliegt, löst die Inhaltsüberprüfung eine <xref:System.NotSupportedException> aus. Im folgenden Beispiel ist der URI-Endpunkt für die Anforderung der Wettervorhersagedaten falsch geschrieben.

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
> Der URI sollte `WeatherForecast` sein, aber wird im Befehl als `WeatherForcast` (fehlendes „e“) angezeigt. Der <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>-Aufruf erwartet, dass JSON zurückgegeben wird, der Server gibt jedoch HTML für einen Ausnahmefehler auf dem Server mit einem `Content-Type` von `text/html` zurück.

Der Ausnahmefehler tritt auf dem Server auf, da der Pfad nicht gefunden wurde und die Middleware keine Seite oder Ansicht für die Anforderung verarbeiten kann.

## <a name="cross-origin-resource-sharing-cors"></a>In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> auf dem Client wird <xref:System.NotSupportedException> ausgelöst, wenn der Inhalt der Antwort als Nicht-JSON-Code überprüft wird.

Die Ausnahme wird im `catch`-Block abgefangen, in dem die benutzerdefinierte Logik den Fehler protokollieren oder eine benutzerfreundliche Fehlermeldung an den Benutzer senden kann. Das vorherige Beispiel dient nur der Veranschaulichung. Eine Web-API-Server-App kann so konfiguriert werden, dass JSON auch dann zurückgegeben wird, wenn kein Endpunkt vorhanden ist oder ein Ausnahmefehler auf dem Server auftritt. Weitere Informationen finden Sie unter <xref:blazor/handle-errors>.

Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.

## <a name="additional-resources"></a>Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.

* Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu stellen, muss der *Endpunkt* die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/) ermöglichen.
* Die [Blazor WebAssembly-Beispiel-App (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) veranschaulicht die Verwendung von CORS in der Komponente „Web-API aufrufen“ (*Pages/CallWebAPI.razor*).
