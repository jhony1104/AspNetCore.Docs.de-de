---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434264"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Hosten und Bereitstellen von ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.
1. Wählen Sie das *Veröffentlichungsziel* aus. Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.
1. Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an. Wählen Sie die Schaltfläche **Veröffentlichen** aus.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:

```dotnetcli
dotnet publish -c Release
```

---

Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.

Veröffentlichungsspeicherorte:

* Blazor WebAssembly
  * Eigenständig &ndash; die App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/wwwroot* veröffentlicht. Zum Bereitstellen der App als statische Website kopieren Sie den Inhalt des Ordners *wwwroot* auf den Host der statischen Website.
  * Gehostet &ndash; der Client Blazor der WebAssembly-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/wwwroot* der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht. Stellen Sie die Inhalte des Ordners *publish* auf dem Host bereit.
* Blazor-Server &ndash; die App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish* veröffentlicht. Stellen Sie die Inhalte des Ordners *publish* auf dem Host bereit.

Die Objekte im Ordner werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="app-base-path"></a>Basispfad einer App

Der *Basispfad einer App* beschreibt den URL-Stammpfad der App. Betrachten Sie die folgende ASP.NET Core-App und die untergeordnete Blazor-App:

* Die ASP.NET Core-App heißt `MyApp`:
  * Die App befindet sich physisch unter *d:/MyApp*.
  * Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.
* Eine Blazor-App mit dem Namen `CoolApp` ist die untergeordnete App von `MyApp`:
  * Die untergeordnete App befindet sich physisch unter *d:/MyApp/CoolApp*.
  * Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.

Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet. Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.

Das `href`-Attribut des Tags `<base>` wird auf den relativen Stammpfad in der Datei *Pages/_Host.cshtml* (Blazor Server) oder *wwwroot/index.html* (Blazor WebAssembly) festgelegt, um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben:

```html
<base href="/CoolApp/">
```

Blazor Server-Apps legen zusätzlich den serverseitigen Basispfad fest, indem sie <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in der Anforderungspipeline `Startup.Configure` der App aufrufen.

```csharp
app.UsePathBase("/CoolApp");
```

Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen. Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen. Ferner wird der Basispfad der App verwendet, um ausgewählte Hyperlinks abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet. Der Router Blazor verarbeitet interne Navigation.

Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App. In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps. Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers der App festgelegt werden.

Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei *Pages/_Host.cshtml* (Blazor Server) oder *wwwroot/index.html* (Blazor WebAssembly), um den Basispfad der App festzulegen. Legen Sie das `href`-Attribut auf `/{RELATIVE URL PATH}/` fest (der nachstehende Schrägstrich ist erforderlich). `{RELATIVE URL PATH}` entspricht hier dem vollständigen relativen URL-Pfad der App.

Bei einer Blazor WebAssembly-App mit einem relativen URL-Pfad, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*. Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht. Fügen Sie keinen nachgestellten Schrägstrich ein. Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Für eine Blazor WebAssembly-App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Die Blazor WebAssembly-App antwortet lokal unter `http://localhost:port/CoolApp`.

## <a name="deployment"></a>Bereitstellung

Anleitungen für die Bereitstellung finden Sie in folgenden Themen:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
