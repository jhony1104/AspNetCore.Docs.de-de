---
title: Erste Schritte mit ASP.NET Core
author: rick-anderson
description: 'Kurztutorial, in dem eine einfache Hello World-App mit ASP.NET Core erstellt und ausgeführt wird.'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2019
uid: getting-started
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Tutorial: Erste Schritte mit ASP.NET Core

Dieses Tutorial zeigt, wie die .NET Core-Befehlszeilenschnittstelle verwendet wird, um eine ASP.NET Core-Web-App zu erstellen.

Sie lernen, die folgende Aufgaben auszuführen:

> [!div class="checklist"]
> * Erstellen Sie ein Web-App-Projekt.
> * Aktivieren Sie lokales HTTPS.
> * Führen Sie die App aus.
> * Bearbeiten Sie eine Razor-Seite.

Am Schluss werden Sie eine funktionierende Web-App auf Ihrem lokalen Computer besitzen.

![Web-App-Startseite](_static/home-page.png)

## <a name="prerequisites"></a>Erforderliche Komponenten

* [.NET Core 2.2 SDK](https://www.microsoft.com/net/download/all)

## <a name="create-a-web-app-project"></a>Erstellen Sie ein Web-App-Projekt.

Öffnen Sie eine Befehlsshell, und geben Sie den folgenden Befehl ein:

```console
dotnet new webapp -o aspnetcoreapp
```

## <a name="enable-local-https"></a>Aktivieren Sie lokales HTTPS.

Vertrauen Sie dem HTTPS-Entwicklungszertifikat:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
dotnet dev-certs https --trust
```

Über den vorherigen Befehl wird der folgende Dialog angezeigt:

![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
dotnet dev-certs https --trust
```

Über den vorherigen Befehl wird die folgende Meldung angezeigt:

*Trusting the HTTPS development certificate was requested. Wenn das Zertifikat nicht bereits vertrauenswürdig, ist führen wir den folgenden Befehl aus:*  `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.

Dieser Befehl fordert Sie möglicherweise zur Eingabe Ihres Kennworts auf, um das Zertifikat in der Keychain für das System zu installieren. Geben Sie Ihr Kennwort ein, wenn Sie die Vertrauenswürdigkeit des Entwicklungszertifikats bestätigen möchten.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Weitere Informationen zum Bestätigen der Vertrauenswürdigkeit eines HTTPS-Entwicklungszertifikats finden Sie in der Dokumentation zu Ihrer Linux-Distribution.

---

Weitere Informationen finden Sie unter [Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikat als vertrauenswürdig](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die folgenden Befehle aus:

```console
cd aspnetcoreapp
dotnet run
```

Nachdem die Befehlsshell angibt, dass die App gestartet wurde, wechseln Sie zu [https://localhost:5001](https://localhost:5001). Klicken Sie auf **Accept** (Akzeptieren), um die Datenschutz- und Cookierichtlinie zu akzeptieren. Diese App bewahrt keine personenbezogenen Informationen auf.

## <a name="edit-a-razor-page"></a>Bearbeiten einer Razor-Seite

Öffnen Sie *Pages/Index.cshtml*, und ändern Sie die Seite mit dem folgenden hervorgehobenen Markup:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Navigieren Sie zu [https://localhost:5001](https://localhost:5001), und überprüfen Sie, ob die Änderungen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen Sie ein Web-App-Projekt.
> * Aktivieren Sie lokales HTTPS.
> * Führen Sie das Projekt aus.
> * Führen Sie eine Änderung durch.

Um mehr über ASP.NET Core zu lernen, machen Sie sich mit dem empfohlenen Lernpfad in der Einführung vertraut:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
