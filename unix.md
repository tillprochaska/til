# Unix


## macOS
macOS basiert auf BSD (kurz für *Berkley Software Distribution*), einem Unix-basierten Betriebssystem. GNU/Linux ist ein Unix-ähnliches Betriebssystem, das aus der Kombination aus Linux-Kernel und dem GNU-Projekt, einer Reihe von Softwarepaketen. Viele Standard-Tools aus BSD und GNU heißen zwar ähnlich und erfüllen auch einen ähnlichen Zweck, sie unterscheiden sich allerdings häufig in Details, was zu Verwirrung führen kann. So liefer beispielsweise `grep` unter macOS häufig andere Ergebnisse als `grep` unter einem GNU/Linux.

## GNU Coreutils (und weitere) installieren
Mit Homebrew können einfach die GNU Core Utilities installiert werden. Das macht vieles leichter, weil die o. g. Unterschiede der macOS Utils wegfallen.

```
brew install coreutils
```

[Alle GNU Core Utilities](https://en.wikipedia.org/wiki/List_of_GNU_Core_Utilities_commands) sind dann mit `g` als Präfix verfügbar. Die GNU-Version von `ls` kann dann also über `gls` ausgeführt werden.

Außerdem praktisch sind die unter `findutils` zusammengefassten Programme sowie `grep` und `sed`, die mit den folgenden Brew-Formeln installiert werden können:

```
brew install findutils grep gnu-sed
```

## GNU grep
* `-E --extended-regex` erlaubt die Verwendung erweiterter regulärer Ausdrücke, z. B. von Quantifiern wie `+` oder `{n,m}`
* `-o --only-matching` gibt nur den auf das Pattern passenden Text aus

## Datei-/Verzeichnisberechtigungen setzen
Mithilfe des Programms `chmod` können Datei- und Verzeichnisberechtigungen gesetzt werden. Dabei können Lese-, Schreib- und Ausführungsrechte für den Besitzer, für Gruppenmitglieder und alle anderen Nutzer getrennt hinzugefügt (`+`) oder entzogen (`-`) werden.

```
chmod [Benutzertyp][+|-][Berechtigung]
```

| Benutzertyp     | Modus |
|-----------------|-------|
| Besitzer (user) | u     |
| Gruppe (group)  | g     |
| Andere (other)  | o     |

| Berechtigung         | Kürzel |
| ---------------------|--------|
| Lese (read)          | r      |
| Schreib (write)      | w      |
| Ausführung (execute) | x      |

### Beispiele
Schreibrechte für den Besitzer hinzufügen:
```
chmod u+w myfile
```

Schreib- und Leserechte für die Gruppe hinzufügen:
```
chmod u+rw myfile
```

Schreib- und Ausführungsrechte für alle anderen entziehen:
```
chmod o-wx myfile
```

## Prozess unter bestimmtem Port finden und beenden
`lsof` steht für »list open files« und ist ein Hilfsprogramm, das Informationen über geöffnete Dateien anzeigt. Da unter Unix-Systemen alles eine Datei ist, kann es für viele Anwendungszwecke hilfrich sein, unter anderem um Herauszufinden, welcher Prozess einen bestimmten Port belegt.

Die Liste kann gefiltert werden, z. B. mithilfe der `-i`-Flag nach Dateien die mit einer bestimmten Internet-Adresse übereinstimmen.

Filtern nach IPv4, TCP-Verbindungen und Port 5000:

```
lsof -i 4TCP:5000
```

Ausgegeben wird eine Tabelle mit verschiedenen Informationen, u. a. mit der Prozess-Id (PID):

```
COMMAND   PID          USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
node    48965 tillprochaska   21u  IPv6 0xc664f5446b99e6d1      0t0  TCP *:hbci (LISTEN)
```

Der Prozess kann dann mithilfe von `kill` beendet werden:

```
kill -9 48965
```

## Symbolische Links
Mit dem `ln`-Programm (kurz für »link«) können neue harte und symbolische Links erstellt werden. Standardmäßig erstellt das Programm harte Links, mit der `-s`-Flag auch symbolische. Harte Links verknüpfen einen Dateinamen mit Inhalten auf dem Dateisystem, während symbolische Links eine andere Datei referenzieren.

Symbolischen Link von `referenced_file.txt` auf `link_file.txt` erstellen:

```
ln -s source_file.txt linked_file.txt
```

## Datei- und Verzeichnisgrößen anzeigen
Das `du`-Programm (kurz für »disk usage«) zeigt die Größe bestimmter oder mehrerer Verzeichnisse an. Hilfreich können auch die folgenden Flags sein:

| Flag | Beschreibung |
|------|--------------|
| `--human-readable` | wandelt die Größe automatisch in einen gut lesbare Einheit, z. B. Megabyte oder Gigabyte um |
| `--total` | zeigt zusätzlich die Gesamtgröße aller Dateien/Verzeichnisse an |

Größe aller Dateien und Verzeichnisse im aktuellen Verzeichnis sowie deren Gesamtgröße anzeigen:

```
du --total --human-readable *
```

## SSH Escape Sequences
Normalerweise werden bei einer aktiven SSH-Session alle Tastenanschläge an den Remote-Computer weitergeleitet. Sollte die Verbindung zum Remote-Computer einmal verloren gehen, kann die SSH-Session dann nicht mehr mit `Ctrl + D` o. ä. beendet werden. Es gibt jedoch eine Reihe von *Escape-Sequenzen*, die mit der `~` beginnen und nicht weitergeleitet, sondern lokal behandelt werden:

```
 ~.   - terminate connection (and any multiplexed sessions)
 ~B   - send a BREAK to the remote system
 ~C   - open a command line
 ~R   - request rekey
 ~V/v - decrease/increase verbosity (LogLevel)
 ~^Z  - suspend ssh
 ~#   - list forwarded connections
 ~&   - background ssh (when waiting for connections to terminate)
 ~?   - this message
 ~~   - send the escape character by typing it twice
(Note that escapes are only recognized immediately after newline.)
```

Die Verbindung kann dann z. B. mit `Enter + ~ + .` beendet werden.

## Execution vs. Sourcing
Es gibt zwei Variante, ein Shell-Script auszuführen. Die erste Variante führt das Script in einem *separaten* Shell-Prozess aus. Variablen, die das aufgerufene Script exportiert, sind aus diesem Grund nicht im aufrufenden Shell-Prozess verfügbar. Dazu wird lediglich der Pfad zu einem ausführbaren Script angegeben.


```shell
# /usr/local/bin/my-script
export ENVIRONMENT=PRODUCTION
```

```cmd
$ /usr/local/bin/my-script
$ echo $ENVIRONMENT
```

Die Ausgabe der obigen Aufrufe ist leer. Die zweite Variante, das sogenannte *Sourcing* führt das Script im *aktuellen* Shell-Prozess aus. Entsprechend sind alle exportierten Variablen dann auch verfügbar:

```cmd
$ source /usr/local/bin/my-script
$ echo $ENVIRONMENT
```

Die obigen Aufrufe geben `PRODUCTION` zurück.
