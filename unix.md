# Unix

* macOS basiert auf BSD (kurz für *Berkley Software Distribution*), einem Unix-basierten Betriebssystem.
* GNU/Linux ist ein unix-ähnliches Betriebssystem, das aus der Kombination aus Linux-Kernel und dem GNU-Projekt, einer Reihe von Softwarepaketen.
* Viele Standard-Tools aus BSD und GNU heißen zwar ähnlich und erfüllen auch einen ähnlichen Zweck, sie unterscheiden sich allerdings häufig in Details, was zu Verwirrung führen kann. So liefer beispielsweise `grep` unter macOS häufig andere Ergebnisse als `grep` unter einem GNU/Linux.

## GNU grep
* `-E --extended-regex` erlaubt die Verwendung erweiterter regulärer Ausdrücke, z. B. von Quantifiern wie `+` oder `{n,m}`
* `-o --only-matching` gibt nur den auf das Pattern passenden Text aus
