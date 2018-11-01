# Vim

## Befehle

### Beenden
`:w` speichert das Dokument. `:q` beendet `vim`. `:wq` (oder das Kürzel `:x`) speichern das Dokument und beenden `vim` anschließend.

## Modi

### Normal Mode
Im normalen Modus kann effizient durch den Text navigiert werden.

#### In den Normal Mode wechseln
<kbd>Esc</kbd> wechselt jederzeit in den Normal Mode.

#### Zwischen Wörtern navigieren
* <kbd>W</kbd> (word): wechselt zum Anfang des nächsten Wortes
* <kbd>W</kbd> (beginning): wechselt zum Anfang des aktuellen Wortes
* <kbd>W</kbd> (end): wechselt zum Ende des aktuellen Wortes

Die Befehle können dabei auch mit vorangehenden Zahlen kombiniert werden. Mit <kbd>3</kbd> + <kbd>W</kbd> sprint der Cursor beispielsweise drei Wörter weiter.

#### Suchen
Im normalen Modus <kbd>/</kbd> und folgend eine beliebige Zeichenkette eingeben, um nach ihr zu suchen.
* <kbd>N</kbd>: wechselt zum nächsten Vorkommen
* <kbd>Shift</kbd> + <kbd>N</kbd>: wechselt zum vorherigen Vorkommen

### Insert Mode
Im Insert Modus kann Text eingegeben/manipuliert werden. In den Insert-Modus kann mit den folgenden Tastaturkürzeln gewechselt werden:

#### In den Insert Mode wechseln
* <kbd>I</kbd> (insert): vor dem Cursor einfügen
* <kbd>A</kbd> (append): nach dem Cursor einfügen
* <kbd>Shift</kbd> + <kbd>I</kbd> (insert): am Anfang der Zeile einfügen
* <kbd>Shift</kbd> + <kbd>A</kbd> (append): am Ende der Zeile einfügen
