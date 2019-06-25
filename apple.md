# Apple

## Zwischenablage synchronisieren
Neuere macOS- und iOS-Versionen können die universelle Zwischenablage verwenden. Damit kann zum Beispiel ein Text auf dem MacBook kopiert und auf dem iPhone eingefügt werden. Voraussetzung ist lediglich, dass die Geräte die gleiche Apple-ID verwenden und Bluetooth und WLAN aktiviert sind.

## Zwischen Fenstern der selben Anwendung wechseln
Mit `Cmd + <` kann zwischen Fenster den selben Anwendung gewechselt werden. Das funktioniert allerdinsg nur, solange sich alle Fenster im selben Workspace befinden – also nicht bei Fenstern im Vollbildmodus.

## `.dev`-Domains werden nicht resolved
Der [Pow Server] für Mac OS hat vor dem Kauf der `.dev`-TLD durch Google diese Endung standardmäßig verwendet und zu diesem Zweck die Datei `/etc/resolver/dev` angelegt. Durch das Anlegen einer gleichlautenden Datei im Verzeichnis `/etc/resolver` können bestimmte DNS-Server für diese Domains erzwungen werden. Das hat sich auch Pow zunutze gemacht, was aber heute zu Konflikten mit registrierten `.dev`-Domains führt.
