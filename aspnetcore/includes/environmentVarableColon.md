Das Trennzeichen `:` funktioniert nicht auf allen Plattformen mit den hierarchischen Schlüsseln von Umgebungsvariablen. Der doppelte Unterstrich `__`:

* wird auf allen Plattformen unterstützt. Das Trennzeichen `:` wird beispielsweise nicht von [Bash](https://linuxhint.com/bash-environment-variables/) unterstützt, `__` hingegen schon.
* automatisch durch `:` ersetzt.