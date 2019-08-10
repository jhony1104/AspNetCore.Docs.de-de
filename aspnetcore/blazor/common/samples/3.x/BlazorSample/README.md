# <a name="blazor-client-side-sample-app"></a>Beispiel-App für blazor (Client seitig)

Dieses Beispiel veranschaulicht die Verwendung von blazor-Szenarios, die in der blazor-Dokumentation beschrieben werden.

## <a name="call-web-api-example"></a>Beispiel für Web-API-Aufrufe

Das Web-API-Beispiel erfordert eine Web-API, die auf der Beispiel <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">-App für das Tutorial basiert: Erstellen Sie eine Web-API mit ASP.net Core</a> MVC-Thema. Die Beispiel-App sendet Anforderungen an die Web- `https://localhost:10000/api/todo`API unter. Wenn eine andere Web-API-Adresse verwendet wird, `ServiceEndpoint` aktualisieren Sie den konstanten Wert im Block `@functions` der Razor-Komponente.</p>

Die Beispiel-App stellt eine <a href="https://docs.microsoft.com/aspnet/core/security/cors">cors-Anforderung (Cross-Origin Resource Sharing)</a> von `http://localhost:5000` oder `https://localhost:5001` zur Web-API her. Anmelde Informationen (Autorisierungs Cookies/-Header) sind zulässig. Fügen Sie die folgende cors-Middleware-Konfiguration zur- `Startup.Configure` Methode der Web- `UseMvc`API hinzu, bevor Sie aufruft:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Passen Sie die Domänen und `WithOrigins` Ports von nach Bedarf für die blazor-APP an.

Die Web-API ist für cors konfiguriert, um die Autorisierung von Cookies/Headern und Anforderungen vom Client Code zuzulassen, aber die Web-API, die im Tutorial erstellt wurde, autorisiert keine Anforderungen. Informationen zur Implementierung finden Sie in den Artikeln zu den <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.net Core Sicherheit und Identität</a> .
