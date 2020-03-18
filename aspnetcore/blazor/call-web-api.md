---
title: Aufrufen einer Web-API über ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie mithilfe von JSON-Hilfsprogramme eine Web-API von einer Blazor-App aufrufen können, einschließlich der Erstellung von CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647731"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Aufrufen einer Web-API über ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Juan De la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)-Apps rufen Web-APIs über einen vorkonfigurierten `HttpClient`-Dienst auf. Erstellen Sie Anforderungen, die JavaScript [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)-Optionen enthalten können, mit BlazorJSON-Hilfsprogrammen oder mit <xref:System.Net.Http.HttpRequestMessage>. Der `HttpClient`-Dienst in Blazor WebAssembly-Apps konzentriert sich darauf, Anforderungen an den Ursprungsserver zurückzusenden. Die Anleitung in diesem Thema bezieht sich nur auf Blazor WebAssembly-Apps.

[Blazor Server](xref:blazor/hosting-models#blazor-server)-Apps rufen Web-APIs über <xref:System.Net.Http.HttpClient>-Instanzen auf, die in der Regel mit <xref:System.Net.Http.IHttpClientFactory> erstellt werden. Die Anleitung in diesem Thema bezieht sich nicht auf Blazor Server-Apps. Befolgen Sie bei der Entwicklung von Blazor Server-Apps die Anleitung in <xref:fundamentals/http-requests>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) &ndash; Wählen Sie die *BlazorWebAssemblySample*-App aus.

Weitere Informationen finden Sie in der *BlazorWebAssemblySample*-Beispiel-App:

* Aufrufen der Web-API (*Pages/CallWebAPI.razor*)
* HTTP-Anforderungstester (*Components/httprequesttester. Razor*)

## <a name="packages"></a>Pakete

Verweisen Sie auf das *experimentelle* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/)-NuGet-Paket in der Projektdatei. `Microsoft.AspNetCore.Blazor.HttpClient` basiert auf `HttpClient` und [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).

Zur Verwendung einer stabilen API verwenden Sie das Paket [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/), das [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) verwendet. Die Verwendung der stabilen API in `Microsoft.AspNet.WebApi.Client` bietet nicht die in diesem Thema beschriebenen JSON-Hilfsprogramme, die nur für das experimentelle `Microsoft.AspNetCore.Blazor.HttpClient`-Paket zur Verfügung stehen.

## <a name="httpclient-and-json-helpers"></a>HttpClient- und JSON-Hilfsprogramme

In einer Blazor WebAssembly-App ist [HttpClient](xref:fundamentals/http-requests) als vorkonfigurierter Dienst für die Rückübertragung von Anforderungen an den Ursprungsserver verfügbar.

Eine Blazor Server-App enthält standardmäßig keinen `HttpClient`-Dienst. Stellen Sie der App über die [HttpClient-Factoryinfrastruktur](xref:fundamentals/http-requests) einen `HttpClient` zur Verfügung.

`HttpClient`- und JSON-Hilfsprogramme werden auch zum Aufrufen von Web-API-Endpunkten von Drittanbietern verwendet. `HttpClient` wird mithilfe der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) des Browsers implementiert und unterliegt dessen Beschränkungen, einschließlich der Durchsetzung derselben Ursprungsrichtlinie.

Die Basisadresse des Clients wird auf die Adresse des Ursprungsservers festgelegt. Fügen Sie eine `HttpClient`-Instanz mit der `@inject`-Direktive ein:

```razor
@using System.Net.Http
@inject HttpClient Http
```

In den folgenden Beispielen erstellt, liest, aktualisiert und löscht eine Todo-Web-API Vorgänge (CRUD). Die Beispiele basieren auf einer `TodoItem`-Klasse, die Folgendes speichert:

* ID (`Id`, `long`) &ndash; Eindeutige ID des Elements.
* Name (`Name`, `string`) &ndash; Name des Elements.
* Status (`IsComplete`, `bool`) &ndash; Angabe, ob das Todo-Element abgeschlossen ist.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON-Hilfsmethoden senden Anforderungen an einen URI (in den folgenden Beispielen eine Web-API) und verarbeiten die Antwort:

* `GetJsonAsync` &ndash; Sendet eine HTTP-GET-Anforderung und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.

  Im folgenden Code werden die `_todoItems` von der Komponente angezeigt. Die `GetTodoItems`-Methode wird ausgelöst, wenn das Rendern der Komponente abgeschlossen ist ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Ein vollständiges Beispiel finden Sie in der Beispiel-App.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; Sendet eine HTTP-POST-Anforderung, einschließlich JSON-codierten Inhalten, und analysiert den JSON-Antworttext, um ein Objekt zu erstellen.

  Im folgenden Code wird `_newItemName` durch ein gebundenes Element der Komponente bereitgestellt. Die `AddItem`-Methode wird durch Auswählen eines `<button>`-Elements ausgelöst. Ein vollständiges Beispiel finden Sie in der Beispiel-App.

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

* `PutJsonAsync` &ndash; Sendet eine HTTP-PUT-Anforderung, einschließlich JSON-codierter Inhalte.

  Im folgenden Code werden `_editItem`-Werte für `Name` und `IsCompleted` durch gebundene Elemente der Komponente bereitgestellt. Die `Id` des Elements wird festgelegt, wenn das Element in einem anderen Teil der Benutzeroberfläche ausgewählt und `EditItem` aufgerufen wird. Die Methode `SaveItem` wird durch Auswahl des `<button>`-Elements „Speichern“ ausgelöst. Ein vollständiges Beispiel finden Sie in der Beispiel-App.

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

<xref:System.Net.Http> enthält zusätzliche Erweiterungsmethoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) wird verwendet, um eine HTTP-DELETE-Anforderung an eine Web-API zu senden.

Im folgenden Code ruft das `<button>`-Element „Löschen“ die `DeleteItem`-Methode auf. Das gebundene `<input>`-Element stellt die `id` des zu löschenden Elements bereit. Ein vollständiges Beispiel finden Sie in der Beispiel-App.

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

## <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat. Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet. Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Um Anforderungen vom Browser an einen Endpunkt mit einem anderen Ursprung zu stellen, muss der *Endpunkt* die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/) ermöglichen.

Die [Blazor WebAssembly-Beispiel-App (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) veranschaulicht die Verwendung von CORS in der Komponente „Web-API aufrufen“ (*Pages/CallWebAPI.razor*).

Informationen darüber, wie Sie anderen Websites ermöglichen, CORS-Anforderungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen) an Ihre App zu stellen, finden Sie unter <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient und HttpRequestMessage mit Fetch-API-Anforderungsoptionen

Wenn Sie WebAssembly in einer Blazor WebAssembly-App ausführen, verwenden Sie [HttpClient](xref:fundamentals/http-requests) und <xref:System.Net.Http.HttpRequestMessage> zum Anpassen von Anforderungen. Sie können z. B. den Anforderungs-URI, die HTTP-Methode und beliebige Anforderungsheader angeben.

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

Weitere Informationen zu den Fetch-API-Optionen finden Sie in den [MDN-Webdokumentationen: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.

Die folgende Richtlinie enthält die Konfiguration für:

* Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).
* Any-Methode (Verb).
* `Content-Type`- und `Authorization`-Header. Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.
* Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (*Components/HTTPRequestTester.razor*).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) beim W3C](https://www.w3.org/TR/cors/)
