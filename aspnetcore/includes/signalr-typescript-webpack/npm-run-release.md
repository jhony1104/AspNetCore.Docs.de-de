---
ms.openlocfilehash: c82571d3cfa57ccd6e7c83f654f119bdd8991486
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210439"
---
```console
npm run release
```

Dieser Befehl hält die clientseitigen Objekte an, die bereitgestellt werden, wenn die App ausgeführt wird. Die Objekte werden im Ordner *wwwroot* gespeichert.

Webpack hat die folgenden Aufgaben durchgeführt:

* Die Inhalte des Verzeichnis *wwwroot* wurden bereinigt.
* TypeScript wurde in JavaScript konvertiert. Dieser Prozess ist als *Transpilierung* bekannt.
* Der generierte JavaScript-Code wurde gekürzt, um die Dateigröße zu reduzieren. Dieser Prozess ist als *Minimierung* bekannt.
* Die verarbeiteten JavaScript-, CSS- und HTML-Dateien wurden aus *src* in das Verzeichnis *wwwroot* kopiert.
* Die folgenden Elemente wurden in die Datei *wwwroot/index.html* eingefügt:
  * Ein `<link>`-Tag, das auf die Datei *wwwroot/main.\<hash\>.css* verweist. Dieses Tag wird unmittelbar vor dem schließenden Tag `</head>` platziert.
  * Ein `<script>`-Tag, das auf die minimierte Datei *wwwroot/main.\<hash\>.js* verweist. Dieses Tag wird unmittelbar vor dem schließenden Tag `</body>` platziert.
