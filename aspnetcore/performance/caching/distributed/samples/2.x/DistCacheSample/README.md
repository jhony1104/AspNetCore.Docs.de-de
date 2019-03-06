# <a name="aspnet-core-distributed-cache-sample"></a>Beispiel: ASP.NET Core verteilten Cache

Dieses Beispiel veranschaulicht die Verwendung eines verteilten Zwischenspeichers an. Dieses Beispiel zeigt die in beschriebenen Szenario die [arbeiten mit einem verteilten Cache in ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) Thema.

In der produktionsumgebung ist die Beispiel-app zur Verwendung von einem verteilten Cache von SQL Server konfiguriert. Ändern Sie die app zur Verwendung einen verteilten Redis-Cache neu konfigurieren, die Präprozessordirektive am oberen Rand der *"Startup.cs"* Datei zur Verwendung von Redis (`#define Redis // SQLServer`). Weitere Informationen finden Sie unter [Präprozessordirektiven im Beispielcode](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code).
