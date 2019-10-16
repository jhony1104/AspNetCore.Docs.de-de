---
title: Web-API aus ASP.net Core blazor aufzurufen
author: guardrex
description: Erfahren Sie, wie Sie eine Web-API über eine blazor-App mithilfe von JSON-Hilfsprogrammen aufzurufen, einschließlich der cors-Anforderungen (Cross-Origin Resource Sharing).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/call-web-api
ms.openlocfilehash: b08fdf5c2f9a523314b1744a33087eb64fa4c14a
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390827"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Web-API aus ASP.net Core blazor aufzurufen

Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)und [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Webassembly-apps rufen Web-APIs mithilfe eines vorkonfigurierten `HttpClient`-Diensts auf. Verfassen von Anforderungen, die JavaScript-Abruf- [API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Optionen mithilfe von blazor JSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage> enthalten können.

Blazor-Server-apps greifen Web-APIs mithilfe von <xref:System.Net.Http.HttpClient>-Instanzen an, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory> Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Beispiele für blazor Webassembly finden Sie unter den folgenden Komponenten in der Beispiel-App:

* Aufrufen der Web-API (*pages/callwebapi. Razor*)
* HTTP-Anforderungs Tester (*Components/httprequesttester. Razor*)

## <a name="httpclient-and-json-helpers"></a>HttpClient-und JSON-Hilfsprogramme

In blazor Webassembly-Apps ist [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst zur Wiederherstellung von Anforderungen an den Ursprungsserver verfügbar. Fügen Sie `Microsoft.AspNetCore.Blazor.HttpClient` einen Paket Verweis hinzu, um `HttpClient`-JSON-Hilfsprogramme zu verwenden. `HttpClient`-und JSON-Hilfsprogramme werden auch verwendet, um Web-API-Endpunkte von Drittanbietern aufzurufen. `HttpClient` wird mithilfe der Browser- [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) implementiert und unterliegt den Einschränkungen, einschließlich der Erzwingung der gleichen Ursprungs Richtlinie.

Die Basisadresse des Clients wird auf die Adresse des Ursprungs Servers festgelegt. Fügen Sie mit der `@inject`-Direktive eine `HttpClient`-Instanz ein:

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

In den folgenden Beispielen verarbeitet eine TODO-Web-API Vorgänge zum Erstellen, lesen, aktualisieren und löschen (CRUD). Die Beispiele basieren auf einer `TodoItem`-Klasse, in der die-Klasse gespeichert wird:

* ID (`Id`, `long`) &ndash; eindeutige ID des Elements.
* Name (`Name`, `string`) &ndash; Name des Elements.
* Status (`IsComplete`, `bool`) &ndash;-Angabe, ob das TODO-Element fertiggestellt ist.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:

* `GetJsonAsync` &ndash; sendet eine HTTP GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.

  Im folgenden Code werden die `_todoItems` von der Komponente angezeigt. Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendering der Komponente abgeschlossen ist ([oninitializedasync](xref:blazor/components#lifecycle-methods)). Ein umfassendes Beispiel finden Sie in der Beispiel-app.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; sendet eine HTTP POST-Anforderung, einschließlich JSON-codiertem Inhalt, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.

  Im folgenden Code wird `_newItemName` von einem gebundenen Element der-Komponente bereitgestellt. Die `AddItem`-Methode wird durch Auswahl eines `<button>`-Elements ausgelöst. Ein umfassendes Beispiel finden Sie in der Beispiel-app.

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
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync` &ndash; sendet eine HTTP PUT-Anforderung, einschließlich JSON-codiertem Inhalt.

  Im folgenden Code werden `_editItem`-Werte für `Name` und `IsCompleted` von gebundenen Elementen der Komponente bereitgestellt. Der `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird. Die `SaveItem`-Methode wird ausgelöst, indem das Save `<button>`-Element ausgewählt wird. Ein umfassendes Beispiel finden Sie in der Beispiel-app.

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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http> enthält zusätzliche Erweiterungs Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten. " [HttpClient. deleteasync](xref:System.Net.Http.HttpClient.DeleteAsync*) " wird verwendet, um eine HTTP DELETE-Anforderung an eine Web-API zu senden.

Im folgenden Code Ruft das DELETE `<button>`-Element die `DeleteItem`-Methode auf. Das gebundene `<input>`-Element stellt den `id` des zu löschenden Elements bereit. Ein umfassendes Beispiel finden Sie in der Beispiel-app.

```cshtml
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

## <a name="cross-origin-resource-sharing-cors"></a>Cross-Origin Resource Sharing (cors)

Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bedient hat. Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet. Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest. Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu senden, muss der *Endpunkt* [Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/)aktivieren.

Die Beispiel-App veranschaulicht die Verwendung von cors in der Web-API-Aufruf Komponente (*pages/callwebapi. Razor*).

Informationen dazu, wie Sie anderen Standorten cors-Anforderungen (Cross-Origin Resource Sharing) an Ihre APP senden können, finden Sie unter <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient und httprequestmessage mit Abruf-API-Anforderungs Optionen

Verwenden Sie zum Anpassen von Anforderungen bei der Ausführung in Webassembly in einer blazor Webassembly-APP [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage>. Sie können z. b. den Anforderungs-URI, die HTTP-Methode und alle gewünschten Anforderungs Header angeben.

Stellen Sie mithilfe der `WebAssemblyHttpMessageHandler.FetchArgs`-Eigenschaft der Anforderung Anforderungs Optionen für die zugrunde liegende JavaScript- [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) bereit. Wie im folgenden Beispiel gezeigt, wird die `credentials`-Eigenschaft auf einen der folgenden Werte festgelegt:

* `FetchCredentialsOption.Include` ("include") &ndash; rät dem Browser, Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) auch für Ursprungs übergreifende Anforderungen zu senden. Nur zulässig, wenn die cors-Richtlinie für das Zulassen von Anmelde Informationen konfiguriert ist.
* `FetchCredentialsOption.Omit` ("weglassen") &ndash; rät dem Browser, niemals Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) zu senden.
* `FetchCredentialsOption.SameOrigin` ("Gleiches Ursprungs") &ndash; weist den Browser an, Anmelde Informationen (z. b. Cookies oder http-Authentifizierungs Header) nur dann zu senden, wenn sich die Ziel-URL auf demselben Ursprung wie die aufrufende Anwendung befindet.

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

Weitere Informationen zu den Abruf-API-Optionen finden Sie unter [MDN-Webdokumentation: windoworworkerglobalscope. FETCH ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Beim Senden von Anmelde Informationen (Autorisierungs Cookies/-Header) für cors-Anforderungen muss der Header "`Authorization`" durch die cors-Richtlinie zugelassen werden.

Die folgende Richtlinie enthält die Konfiguration für:

* Anforderungs Ursprünge (`http://localhost:5000`, `https://localhost:5001`).
* Beliebige Methode (Verb).
* `Content-Type`-und `Authorization`-Header. Um einen benutzerdefinierten Header (z. b. `x-custom-header`) zuzulassen, Listen Sie den-Header auf, wenn Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> aufrufen.
* Anmelde Informationen, die vom Client seitigen JavaScript-Code festgelegt wurden (`credentials`-Eigenschaft ist auf `include` festgelegt).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Weitere Informationen finden Sie unter <xref:security/cors> und der Komponente "http-Anforderungs Tester" der Beispiel-app (*Components/httprequesttester. Razor*).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfiguration des Kestrel-HTTPS-Endpunkts](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross-Origin Resource Sharing (cors) bei W3C](https://www.w3.org/TR/cors/)
