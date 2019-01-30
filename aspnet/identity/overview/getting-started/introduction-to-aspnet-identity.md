---
uid: identity/overview/getting-started/introduction-to-aspnet-identity
title: Einführung zu ASP.NET Identity | Microsoft-Dokumentation
author: jongalloway
description: Das ASP.NET-Mitgliedschaftssystem wurde mit ASP.NET 2.0 zurück und seit 2005 eingeführt und dann gab es viele Änderungen in die Möglichkeiten Web-Applikationen Neustarts...
ms.author: riande
ms.date: 01/22/2019
ms.assetid: 38717fc1-5989-43cf-952d-4007cc1dd923
msc.legacyurl: /identity/overview/getting-started/introduction-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 4a545e52d2d9ea04a10c37c116fd326c60de9f8f
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236444"
---
<a name="introduction-to-aspnet-identity"></a>Einführung zu ASP.NET Identity
====================

> Das ASP.NET-Mitgliedschaftssystem wurde mit ASP.NET 2.0 zurück und seit 2005 eingeführt und dann gab es viele Änderungen in die Möglichkeiten, Webanwendungen in der Regel Authentifizierung und Autorisierung behandeln. ASP.NET Identity ist ein neuer Blickwinkel auf, was das Mitgliedschaftssystem werden soll, wenn Sie moderne Anwendungen für das Web, Telefon oder Tablet erstellen.


## <a name="background-membership-in-aspnet"></a>Hintergrund: Mitgliedschaft in ASP.NET

### <a name="aspnet-membership"></a>ASP.NET-Mitgliedschaft

[ASP.NET-Mitgliedschaft](https://msdn.microsoft.com/library/yh26yfzy(v=VS.100).aspx) wurde entworfen, um die Mitgliedschaft von websiteanforderungen zu lösen, die häufig in 2005 wurden die Formularauthentifizierung und SQL Server-Datenbank für den Benutzernamen und Kennwörter, Profildaten beteiligt. Heute gibt es eine viel größere Palette an Optionen für die datenspeicherung für Webanwendungen ist, und die meisten Entwickler ihre Websites mit Identitätsanbietern aus sozialen Netzwerken für die Authentifizierung und Autorisierung aktiviert werden soll. Die Einschränkungen des Entwurfs für die ASP.NET-Mitgliedschaft erschweren diese Umstellung:

- Das Datenbankschema wurde für SQL Server entwickelt, und Sie ihn nicht ändern. Sie können Informationen zum Profil hinzufügen, aber die zusätzlichen Daten in eine andere Tabelle, die für den Zugriff auf Mitteln außer über die Profil-Ressourcenanbieter-API durch erschwert gepackt werden.
- Das anbietersystem können Sie der Sicherungsspeicher für die Daten zu ändern, aber das System wurde entworfen, um Annahmen, die für eine relationale Datenbank geeignet. Sie können einen Anbieter zum Speichern von Informationen zur Mitgliedschaft in einer nicht-relationalen Speichermechanismus, z.B. Azure Storage-Tabellen, schreiben, aber müssen Sie das Design der relationalen umgehen können, indem Sie das Schreiben von viel Code und die meiste `System.NotImplementedException` für Methoden, die nicht-Ausnahmen gelten Sie für NoSQL-Datenbanken.
- Da die Log-in-Out/Log-Funktion, die sich auf die Formularauthentifizierung basiert, kann nicht das Mitgliedschaftssystem verwenden [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md). OWIN enthält middlewarekomponenten für die Authentifizierung, einschließlich der Unterstützung für Anmeldungen, die mit externen Identitätsanbietern (wie z. B. Microsoft Accounts, Facebook, Google, Twitter), und Anmeldungen, die Verwendung von Organisationskonten aus lokalen Active Directory oder Azure Active Directory. OWIN enthält außerdem Unterstützung für OAuth 2.0, JWT und CORS.

### <a name="aspnet-simple-membership"></a>Einfache Mitgliedschaft

[Einfache ASP.NET-Mitgliedschaft](../../../web-pages/overview/security/16-adding-security-and-membership.md) als ein Mitgliedschaftssystem für ASP.NET Web Pages entwickelt wurde. Es wurde mit WebMatrix und Visual Studio 2010 SP1 veröffentlicht. Das Ziel der einfachen Mitgliedschaft wurde zu erleichtern Mitgliedschaftsfunktionen eine Webseiten-Anwendung hinzu.

Einfache Mitgliedschaft erleichtern es, Informationen aus Benutzerprofilen, anpassen, aber nutzt weiterhin die anderen Probleme mit ASP.NET-Mitgliedschaft, und es gelten einige Einschränkungen:

- Es war schwierig, die Daten der Benutzergruppenmitgliedschaft System in einem nicht-relationalen Speicher beizubehalten.
- Sie können nicht mit OWIN verwenden.
- Es funktioniert nicht gut mit vorhandenen ASP.NET Mitgliedschaftsanbieter ein, und ist nicht erweiterbar.

### <a name="aspnet-universal-providers"></a>ASP.NET-Universelle Anbieter

[ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) wurden entwickelt, um zu ermöglichen, Informationen zur Mitgliedschaft im Microsoft Azure SQL-Datenbank, und sie funktionieren auch mit SQL Server Compact beizubehalten. Universal Providers wurden auf Entity Framework Code First erstellt, was bedeutet, dass der Universal Providers zur datenaufbewahrung in einen beliebigen Speicher unterstützt, die von EF verwendet werden kann. Mit der Universal Providers wurde einiges sowie das Schema der Datenbank bereinigt.

Universal Providers werden basierend auf der ASP.NET-Mitgliedschaft-Infrastruktur erstellt werden, damit sie weiterhin die gleichen Einschränkungen wie der Anbieter SqlMembership ausführen. D.h., sie für relationale Datenbanken konzipiert wurden, und es ist schwierig, Profil und die Benutzerinformationen anpassen. Diese Anbieter verwenden der Formularauthentifizierung auch noch für die an- und Abmeldung Funktionalität.

## <a name="aspnet-identity"></a>ASP.NET Identity

Textabschnitt in ASP.NET hat im Laufe der Jahre, entwickelt das ASP.NET-Team hat eine Menge von Feedback von Kunden wissen, wie die Mitgliedschaft.

Die Annahme, die Benutzeranmeldung wird durch Eingabe von Benutzername und Kennwort, die sie in Ihrer eigenen Anwendung registriert haben, ist nicht mehr gültig. Das Web verfügt über weitere soziale Netzwerke werden. Benutzer interagieren in Echtzeit über soziale Medien wie Facebook, Twitter und anderen Websites sozialer Netzwerke miteinander. Entwickler möchten die Benutzer sich mit ihren sozialen Identitäten anmelden können, damit sie eine komfortable auf ihren Websites haben können. Ein modernes Mitgliedschaftssystem muss Umleitung basierende Anmeldungen an den Authentifizierungsanbieter wie z. B. Facebook, Twitter und andere aktivieren.

Wie Web-Entwicklung entwickelt, haben also die Muster der Webentwicklung. Einheit des Anwendungscodes testen wurde eine Sorge für Anwendungsentwickler. In 2008 hinzugefügt, ASP.NET ein neues Framework basierend auf dem Model-View-Controller (MVC)-Muster, Teil, um Entwicklern das Erstellen von Komponententests getestet werden ASP.NET-Anwendungen erleichtern. Einheit mussten Entwickler Testen ihrer Anwendungslogik zudem soll demonstriert werden, um dies mit das Mitgliedschaftssystem zu können.

Unter Berücksichtigung dieser Änderungen in der Entwicklung von Webanwendungen wurde entwickelt, ASP.NET Identity mit den folgenden Zielen:

- **Eine ASP.NET Identity-system**

    - ASP.NET Identity kann mit allen ASP.NET Frameworks, wie ASP.NET MVC, Web Forms, Webseiten, Web-API und SignalR verwendet werden.
    - ASP.NET Identity kann verwendet werden, wenn Sie Web "," Phone "," Store "oder" Hybrid-Anwendungen erstellen.
- **Einfache unter Verwendung der Profildaten über den Benutzer**

    - Sie haben die Kontrolle über das Schema der Benutzer- und Profilinformationen. Beispielsweise können Sie ganz einfach aktivieren das System zum Speichern von Herstellungsdatum, die vom Benutzer eingegeben wird, wenn sie ein Konto in Ihrer Anwendung registrieren.

- **Persistenz-Steuerelement**

    - In der Standardeinstellung speichert die ASP.NET Identity-System die Benutzerinformationen in einer Datenbank. ASP.NET Identity, Entity Framework Code First verwendet, um alle persistenzmechanismus implementiert werden.
    - Da Sie steuern das Datenbankschema, allgemeine Aufgaben wie das Ändern von Tabellennamen oder zum Ändern ist der Datentyp der primären Schlüssel einfach ist.
    - Es ist einfach, unterschiedliche Mechanismen wie z. B. SharePoint, Azure Storage-Tabellendienst, NoSQL-Datenbanken usw. einbinden, ohne Auslösen `System.NotImplementedExceptions` Ausnahmen.
- **Komponententests**

    - ASP.NET Identity trifft die Webanwendung besser getestet werden. Sie können Komponententests für die Teile der Anwendung schreiben, die ASP.NET Identity verwenden.
- **Rollenanbieter**

    - Es gibt ein Rollenanbieter, der Sie Zugriff auf Teile der Anwendung nach Rollen beschränken kann. Sie können problemlos Rollen wie z. B. "Admin" zu erstellen und Hinzufügen von Benutzern zu Rollen.
- **Anspruchsbasierte**

    - ASP.NET Identity unterstützt die anspruchsbasierte Authentifizierung verwendet werden, wo die Identität des Benutzers als einen Satz von Ansprüchen dargestellt wird. Ansprüche können Entwickler viel ausdrucksvoller beschreiben Sie die Identität eines Benutzers als Rollen ermöglichen. Während der Mitgliedschaft in Datenbankrolle nur einen booleschen Wert ("Member" oder "nicht-Member") ist, kann ein Anspruch umfassende Informationen zu Identität und Gruppenmitgliedschaft des Benutzers enthalten.
- **Anbieter von sozialen Netzwerken basierende Anmeldung**

    - Sie können problemlos soziale Anmeldungen wie Microsoft Account, Facebook, Twitter, Google und anderen Benutzern Ihrer Anwendung hinzufügen und die benutzerspezifischen Daten in Ihrer Anwendung speichern.

- **OWIN-Integration**

    - Authentifizierung in ASP.NET basiert jetzt auf OWIN-Middleware, die auf einem OWIN-basierten Host verwendet werden können. ASP.NET Identity verfügt keine Abhängigkeit auf "System.Web". Es ist ein vollständig kompatibel OWIN-Framework und kann in jede OWIN-gehosteten Anwendung verwendet werden.
    - ASP.NET Identity verwendet OWIN-Authentifizierung für Protokoll-in/Abmeldung von Benutzern der Website an. Dies bedeutet, dass anstatt FormsAuthentication, um das Cookie zu generieren, die Anwendung OWIN CookieAuthentication verwendet, um dies.
- **NuGet-Paket**

    - ASP.NET Identity ist als NuGet-Paket verteilt, die in den Vorlagen für ASP.NET MVC, Web Forms und Web-API installiert ist, die mit Visual Studio 2017 ausgeliefert. Sie können dieses NuGet-Paket in der NuGet Gallery herunterladen.
    - Freigeben von ASP.NET Identity als ein NuGet erleichtert Paket für das ASP.NET-Team beurteilen Sie den neuen Features und Programmfehlerbehebungen und übermittelt diese für Entwickler auf agile Weise.

## <a name="get-started-with-aspnet-identity"></a>Erste Schritte mit ASP.NET Identity

ASP.NET Identity ist in der Visual Studio 2017-Projektvorlagen für ASP.NET MVC, Web Forms, Web-API und SPA verwendet. In dieser exemplarischen Vorgehensweise werden wir veranschaulichen, wie die Projektvorlagen für ASP.NET Identity verwenden, die Funktionalität zum Registrieren, melden Sie sich, und melden Sie sich ein Benutzer hinzufügen.

ASP.NET Identity ist mit dem folgenden Verfahren implementiert. Der Zweck dieses Artikels ist, erhalten Sie eine allgemeine Übersicht über ASP.NET Identity. Sie führen Sie schrittweise durch dieses Verfahren oder nur die Details für das Lesen. Ausführlichere Anweisungen zum Erstellen von apps mithilfe von ASP.NET Identity, einschließlich der Verwendung der neuen API zum Hinzufügen von Benutzern, Rollen und Profilinformationen, finden Sie im Abschnitt "Nächste Schritte" am Ende dieses Artikels.

1. Erstellen Sie eine ASP.NET MVC-Anwendung durch individuelle Konten. Sie können ASP.NET Identity in ASP.NET MVC, Web Forms, Web-API, SignalR usw. verwenden. In diesem Artikel beginnen wir mit einer ASP.NET MVC-Anwendung.  
  
    ![](introduction-to-aspnet-identity/_static/image1.png)
2. Das erstellte Projekt enthält die folgenden drei Pakete für ASP.NET Identity.

    - [`Microsoft.AspNet.Identity.EntityFramework`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/)  
   Dieses Paket verfügt über die Entity Framework-Implementierung von ASP.NET Identity, die die ASP.NET Identity-Daten und das Schema mit SQL Server beibehalten werden.
    - [`Microsoft.AspNet.Identity.Core`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Core/)  
   Dieses Paket enthält die Core-Schnittstellen für ASP.NET Identity. Dieses Paket kann verwendet werden, einer Implementierung für ASP.NET Identity zu schreiben, dass Ziele anderen Persistenz wie z. B. Azure Table Storage, NoSQL-Datenbanken usw. gespeichert.
    - [`Microsoft.AspNet.Identity.OWIN`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Owin/)  
   Dieses Paket enthält Funktionen, die verwendet wird, um die OWIN-Authentifizierung mit ASP.NET Identity in ASP.NET-Anwendungen einbinden. Wird verwendet, wenn Sie zu Ihrer Anwendung und der Aufruf an OWIN Cookie Authentication Middleware Cookies generieren Anmelden an der Funktionalität hinzufügen.
3. Erstellen eines Benutzers an.  
   Starten Sie die Anwendung, und klicken Sie dann auf die **registrieren** Link zum Erstellen eines Benutzers. Die folgende Abbildung zeigt die Seite "registrieren", die den Benutzernamen und das Kennwort erfasst.  
  
    ![](introduction-to-aspnet-identity/_static/image2.png)  
  
   Wenn der Benutzer wählt die **registrieren** Schaltfläche der `Register` Aktion des kontocontrollers erstellt den Benutzer durch Aufrufen der mit ASP.NET Identity-API, wie unten markiert:

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample1.cs?highlight=8-9)]
4. Anmelden.  
   Wenn der Benutzer erfolgreich erstellt wurde, er angemeldet ist die `SignInAsync` Methode.  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample6.cs?highlight=12)]


   Die `SignInManager.SignInAsync` Methode generiert eine ["ClaimsIdentity"](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). Da ASP.NET Identity und OWIN Cookie Authentication anspruchsbasiertes System sind, erfordert das Framework die app, die eine "ClaimsIdentity" für den Benutzer zu generieren. "ClaimsIdentity" enthält Informationen dazu, alle Ansprüche für den Benutzer, wie z. B. welche Rollen, die der Benutzer gehört.   
 
5. Melden Sie sich ab.  
   Wählen Sie die **Abmelden** Verknüpfung, um die Abmeldung-Aktion im Kontocontroller aufzurufen. 

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample5.cs?highlight=6)]

   Der oben hervorgehobene code zeigt die OWIN `AuthenticationManager.SignOut` Methode. Dies ist vergleichbar mit [FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx) Methode fest, mit der [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) -Modul in Web Forms.

## <a name="components-of-aspnet-identity"></a>Komponenten von ASP.NET Identity

Das folgende Diagramm zeigt die Komponenten des ASP.NET Identity-Systems (Wählen Sie im [dies](introduction-to-aspnet-identity/_static/image3.png) oder auf das Diagramm, um es zu vergrößern). Die Pakete in Grün ist das ASP.NET Identity-System bilden. Alle anderen Pakete werden Abhängigkeiten, die erforderlich sind, um das ASP.NET Identity-System in ASP.NET-Anwendungen verwendet kann.

[![](introduction-to-aspnet-identity/_static/image5.png)](introduction-to-aspnet-identity/_static/image4.png)

Im folgenden finden eine kurze Beschreibung des NuGet-Pakete, die nicht erwähnten:

- [Microsoft.Owin.Security.Cookies](http://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/)  
 Middleware, die eine Anwendung zur Verwendung von Cookies ermöglicht-basierte Authentifizierung, ASP ähnelt. NET Formularauthentifizierung.
- [EntityFramework](http://www.nuget.org/packages/EntityFramework/)  
 Entitätsframework ist von Microsoft empfohlene datenzugriffstechnologie für relationale Datenbanken.

## <a name="migrating-from-membership-to-aspnet-identity"></a>Migrieren von Mitgliedschaft zu ASP.NET Identity

Wir hoffen, Sie bald bieten eine Anleitung zur Migration Ihrer vorhandenen apps, die ASP.NET oder einfache Mitgliedschaft auf das neue ASP.NET Identity-System verwenden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer ASP.NET MVC 5-App mit Facebook und Google OAuth2 und OpenID-Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)  
 Das Tutorial verwendet die ASP.NET Identity-API zum Hinzufügen von Profilinformationen mit der Benutzerdatenbank her und wie Sie mit Google und Facebook zu authentifizieren.
- [Erstellen einer ASP.NET MVC-app mit Authentifizierung und SQL-Datenbank und Bereitstellen in Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)  
 Dieses Tutorial veranschaulicht, wie die Identity-API zu verwenden, um Benutzer und Rollen hinzuzufügen.
- [https://github.com/rustd/AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample)  
 Eine beispielanwendung, die zeigt, wie Sie grundlegende Rollen und Unterstützung für Benutzer hinzufügen und wie Sie Rollen und benutzerverwaltung.
