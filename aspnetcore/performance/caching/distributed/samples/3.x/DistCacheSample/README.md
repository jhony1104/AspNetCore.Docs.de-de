# <a name="aspnet-core-distributed-cache-sample"></a>Beispiel für ASP.net Core verteilten Cache

In diesem Beispiel wird die Verwendung eines verteilten Caches veranschaulicht. Dieses Beispiel veranschaulicht das Szenario, das im Thema [Arbeiten mit einem verteilten Cache in ASP.net Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) beschrieben wird.

In der Produktionsumgebung ist die Beispiel-APP so konfiguriert, dass Sie einen verteilten SQL Server Cache verwendet. Um die APP für die Verwendung eines verteilten redis Cache neu zu konfigurieren, ändern Sie die Präprozessordirektive am Anfang der *Startup.cs* -Datei,`#define Redis // SQLServer`um redis () zu verwenden. Weitere Informationen finden Sie unter [Präprozessordirektiven im Beispielcode](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code).
