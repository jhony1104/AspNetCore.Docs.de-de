---
title: Einführung in die Autorisierung in ASP.net Core
author: rick-anderson
description: Erlernen Sie die Grundlagen der Autorisierung und die Funktionsweise der Autorisierung in ASP.net Core apps.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: b5e60b3c256941fff5e54e1a02e077c34c535902
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693868"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Einführung in die Autorisierung in ASP.net Core

<a name="security-authorization-introduction"></a>

Die Autorisierung bezieht sich auf den Prozess, mit dem bestimmt wird, was ein Benutzer tun kann. Ein Administrator kann z. b. eine Dokumentbibliothek erstellen, Dokumente hinzufügen, Dokumente bearbeiten und löschen. Ein nicht administrativer Benutzer, der mit der Bibliothek arbeitet, ist nur zum Lesen der Dokumente autorisiert.

Die Autorisierung ist orthogonal und unabhängig von der Authentifizierung. Die Autorisierung erfordert jedoch einen Authentifizierungsmechanismus. Die Authentifizierung ist der Prozess, bei dem ermittelt wird, wer ein Benutzer ist. Die Authentifizierung kann mindestens eine Identität für den aktuellen Benutzer erstellen.

Weitere Informationen zur Authentifizierung in ASP.net Core finden Sie unter <xref:security/authentication/index>.

## <a name="authorization-types"></a>Autorisierungs Typen

ASP.net Core Autorisierung bietet eine einfache, deklarative und umfassende [Richtlinien basierte](xref:security/authorization/policies) Modell [Funktion](xref:security/authorization/roles) . Die Autorisierung wird in Anforderungen ausgedrückt, und Handler Werten die Ansprüche eines Benutzers anhand der Anforderungen aus. Imperative Überprüfungen können auf einfachen Richtlinien oder Richtlinien basieren, die sowohl die Benutzeridentität als auch die Eigenschaften der Ressource auswerten, auf die der Benutzer zugreifen möchte.

## <a name="namespaces"></a>-Namespaces

Autorisierungs Komponenten, einschließlich der `AuthorizeAttribute`-und `AllowAnonymousAttribute` Attribute, finden Sie im `Microsoft.AspNetCore.Authorization`-Namespace.

Informieren Sie sich in der Dokumentation über die [einfache Autorisierung](xref:security/authorization/simple).
