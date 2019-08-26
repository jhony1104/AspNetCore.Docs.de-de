# <a name="aspnet-core-url-rewriting-sample"></a>Beispiel für die URL-Umschreibung in ASP.NET Core

Dieses Beispiel veranschaulicht die Verwendung der URL umschreibenden Middleware in ASP.NET Core. In der App werden die Optionen zum Umleiten und Neuschreiben von URLs demonstriert.

Beim Ausführen des Beispiels wird die neu geschriebene oder umgeleitete URL von Antworten ohne Datei zurückgegeben, wenn eine der Regeln auf eine Anforderungs-URL angewendet wird. In den Beispielen für XML- und Textdateien, wird die Datei von der Middleware für statische Dateien verarbeitet, nachdem die Anforderungs-URL von der Middleware umgeschrieben wurde.

## <a name="examples-in-this-sample"></a>Beispiele

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Statuscode für Erfolg: 302 Found (302 Gefunden)
  - Beispiel (Umleitung): **/redirect-rule/{capture_group}** zu **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Statuscode für Erfolg: 200 (OK)
  - Beispiel (Umschreibung): **/rewrite-rule/{capture_group_1}/{capture_group_2}** in **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Statuscode für Erfolg: 302 Found (302 Gefunden)
  - Beispiel (Umleitung): **/apache-mod-rules-redirect/{capture_group}** zu **/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Statuscode für Erfolg: 200 (OK)
  - Beispiel (Umschreibung): **/iis-rules-rewrite/{capture_group}** in **/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Statuscode für Erfolg: 301 Moved permanently (301 Permanent verschoben)
  - Beispiel (Umleitung): **/file.xml** zu **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Statuscode für Erfolg: 200 (OK)
  - Beispiel (Neuschreibung): **/some_file.txt** zu **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Statuscode für Erfolg: 301 Moved permanently (301 Permanent verschoben)
  - Beispiel (Umleitung): **/image.png** zu **/png-images/image.png**
  - Beispiel (Umleitung): **/image.jpg** zu **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>PhysicalFileProvider

Sie können auch ein `IFileProvider`-Objekt abrufen, indem Sie ein `PhysicalFileProvider`-Objekt erstellen, das an die Methoden `AddApacheModRewrite()` und `AddIISUrlRewrite()` übergeben wird:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Sichere Umleitungserweiterungen

Dieses Beispiel enthält eine `WebHostBuilder`-Konfiguration für die App zur Verwendung von URLs (`https://localhost:5001`, `https://localhost`) und ein Testzertifikat (*testCert.pfx*) als Unterstützung beim Erkunden der sicheren Umleitungsmethoden. Wenn dem Server Port 443 bereits zugewiesen wurde oder dieser verwendet wird, funktioniert das `https://localhost`-Beispiel nicht. &mdash; Entfernen Sie `ListenOptions` für Port 443 in der `CreateWebHostBuilder`-Methode der *Program.cs*-Datei oder lösen Sie Port 443 aus dem Server heraus, sodass der Port von Kestrel verwendet werden kann.

| Methode                           | Statuscode |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | NULL (465) |
| `.AddRedirectToHttps()`          |     302     | NULL (465) |
| `.AddRedirectToHttps(301)`       |     301     | NULL (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
