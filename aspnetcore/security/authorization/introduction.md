---
title: Einführung in die Autorisierung in ASP.net Core
author: rick-anderson
description: Erlernen Sie die Grundlagen der Autorisierung und die Funktionsweise der Autorisierung in ASP.net Core apps.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: 241ef8b00e9dcbd1983d32edcd9c1db2eaa5c687
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777526"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Einführung in die Autorisierung in ASP.net Core

<a name="security-authorization-introduction"></a>

Die Autorisierung bezieht sich auf den Prozess, mit dem bestimmt wird, was ein Benutzer tun kann. Ein Administrator kann z. b. eine Dokumentbibliothek erstellen, Dokumente hinzufügen, Dokumente bearbeiten und löschen. Ein nicht administrativer Benutzer, der mit der Bibliothek arbeitet, ist nur zum Lesen der Dokumente autorisiert.

Die Autorisierung ist orthogonal und unabhängig von der Authentifizierung. Die Autorisierung erfordert jedoch einen Authentifizierungsmechanismus. Die Authentifizierung ist der Prozess, bei dem ermittelt wird, wer ein Benutzer ist. Die Authentifizierung kann mindestens eine Identität für den aktuellen Benutzer erstellen.

Weitere Informationen zur Authentifizierung in ASP.net Core finden <xref:security/authentication/index>Sie unter.

## <a name="authorization-types"></a>Autorisierungs Typen

ASP.net Core Autorisierung bietet eine einfache, deklarative und umfassende [Richtlinien basierte](xref:security/authorization/policies) Modell [Funktion](xref:security/authorization/roles) . Die Autorisierung wird in Anforderungen ausgedrückt, und Handler Werten die Ansprüche eines Benutzers anhand der Anforderungen aus. Imperative Überprüfungen können auf einfachen Richtlinien oder Richtlinien basieren, die sowohl die Benutzeridentität als auch die Eigenschaften der Ressource auswerten, auf die der Benutzer zugreifen möchte.

## <a name="namespaces"></a>Namespaces

Autorisierungs Komponenten, einschließlich `AuthorizeAttribute` des `AllowAnonymousAttribute` -Attributs und des- `Microsoft.AspNetCore.Authorization` Attributs, finden Sie im-Namespace.

Informieren Sie sich in der Dokumentation über die [einfache Autorisierung](xref:security/authorization/simple).
