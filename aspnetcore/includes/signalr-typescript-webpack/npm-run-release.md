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
