---
title: author: description: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="overview-of-aspnet-core-security"></a>Übersicht über die ASP.NET Core-Sicherheit

ASP.NET Core ermöglicht es Entwicklern, die Sicherheit ihrer Apps einfach zu konfigurieren und zu verwalten. ASP.NET Core umfasst Features für die Verwaltung von Authentifizierung, Autorisierung, Datenschutz, HTTPS-Erzwingung, App-Geheimnissen, Schutz vor Anforderungsfälschung und CORS-Verwaltung. Diese Sicherheitsfunktionen ermöglichen es Ihnen, robuste und dennoch sichere ASP.NET Core-Apps zu erstellen.

## <a name="aspnet-core-security-features"></a>ASP.NET Core-Sicherheitsfeatures

ASP.NET Core stellt zahlreiche Tools und Bibliotheken zur Verfügung, um Ihre Apps zu sichern (einschließlich integrierter Identitätsanbieter). Sie können jedoch auch Identitätsdienste von Drittanbietern wie Facebook, Twitter und LinkedIn nutzen. Mit ASP.NET Core können Sie ganz einfach App-Geheimnisse verwalten, die eine Möglichkeit darstellen, vertrauliche Informationen zu speichern und zu verwenden, ohne sie im Code offenlegen zu müssen.

## <a name="authentication-vs-authorization"></a>Authentifizierung im Vergleich zu Autorisierung

Authentifizierung ist ein Vorgang, bei dem ein Benutzer Anmeldeinformationen bereitstellt, die dann mit den Angaben verglichen werden, die in einem Betriebssystem, einer Datenbank, einer App oder einer Ressource gespeichert sind. Wenn diese übereinstimmen, authentifizieren sich die Benutzer erfolgreich und können dann während eines Autorisierungsprozesses Aktionen ausführen, für die sie berechtigt sind. Die Autorisierung bezieht sich auf den Prozess, der festlegt, welche Aktionen ein Benutzer ausführen darf.

Eine weitere Möglichkeit, Authentifizierung zu definieren, besteht darin, sie als eine Möglichkeit zu betrachten, einen „Raum“ (z.B. einen Server, eine Datenbank, eine App oder eine Ressource) zu betreten, während die Autorisierung darin besteht, welche Aktionen der Benutzer mit welchen Objekten innerhalb dieses „Raums“ (Server, Datenbank oder App) ausführen kann.

## <a name="common-vulnerabilities-in-software"></a>Häufige Sicherheitsrisiken in Software

ASP.NET Core und EF enthalten Features, die Ihnen helfen, Ihre Anwendungen zu schützen und Sicherheitsverletzungen zu verhindern. Die folgende Liste von Links verweist auf die Dokumentation, die Techniken zur Vermeidung der häufigsten Sicherheitsrisiken in Web-Apps beschreibt:

* [XSS-Angriffe (Cross-Site Scripting)](xref:security/cross-site-scripting)
* [Angriffe durch Einschleusung von SQL-Befehlen](/ef/core/querying/raw-sql)
* [Websiteübergreifende Anforderungsfälschung (CSRF)](xref:security/anti-request-forgery)
* [Offene Weiterleitungsangriffe](xref:security/preventing-open-redirects)

Es gibt weitere Sicherheitsrisiken, die Sie kennen sollten. Weitere Informationen finden Sie in den Artikeln im Abschnitt **Security and Identity** (Sicherheit und Identität) im Inhaltsverzeichnis.
