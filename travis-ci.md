# Travis CI

## Monorepo-Setup
Travis betrachtet von Haus aus ein Repository als ein zu testendes Projekt/Package. Es ist dennoch dank des Matrix-Features leicht möglich, mehrere Packages in einem Repository zu testen:

```yaml
# .travis.yml

language: node_js

node_js:
    - "10"
    - "9"

env:
    - PACKAGE_DIR=./package-a
    - PACKAGE_DIR=./package-b

before_install: cd $PACKAGE_DIR
install: npm install
script: npm run test
```

Travis erweitert die »Matrix« der möglichen Umgebungsfaktoren – in diesem Fall sind es die Node.js-Version (2 Optionen) und die verschiedenen Werte der Umgebungsvariable `PACKAGE_DIR` (2 Optionen). Travis testet jede mögliche Kombination separat, führt also die Tests einmal unter Version 9 und einmal unter Version 10 in beiden Paketverzeichnissen aus.

Offensichtlich ist die Lösung in dieser simplen Form für für Monorepos geeignet, die aus Paketen bestehen, die größtenteil die gleichen Sprach-, Build- und Testvoraussetzungen haben.

## Einfaches Deployment-Setup
Travis unterstützt das Deployment zu einer Reihe von Cloud-Providern. Um allerdings einfach nur das Ergebnis des Build-Prozesses zu deployen, reicht `rsync` aus. `rsync` überträgt Dateien per SSH auf einen Zielserver, wobei nur Dateien übertragen werden, die neu hinzugekommen sind oder verändert wurden. Die folgende Konfiguration setzt folgende Umgebungsvariablen voraus, die am besten über die Repository-Einstellung im Travis-Webinterface hinzugefügt und ggf. verschlüsselt werden sollten.

* `TARGET_PUB_KEY`: Public Key des Zielservers, base64-kodiert
* `TARGET_DEPLOY_KEY`: Private Key mit Zugriffsrechten auf Zielserver, base64-kodiert
* `TARGET_USER`: Nutzer des Zielservers
* `TARGET_HOST`: Host des Zielservers

```yaml
# .travis.yml

# Die Befehle in `after_success` werden nur ausgeführt, wenn alle
# vorherigen Schritte erfolgreich verliefen.
after_success:

    - # Dekodiert Publik Key des Zielservers, fügt zu den Known Hosts hinzu
      echo "${TARGET_PUB_KEY}" | base64 --decode >> $HOME/.ssh/known_hosts
      
    - # Dekodiert den Private Key, hinterlegt ihn im Standard-Verzeichnis
      echo "${TARGET_DEPLOY_KEY}" | base64 --decode > $HOME/.ssh/deploy_rsa
      
    - # Stellt sicher, dass der Private Key von keinem anderen Benutzer gelesen
      # oder überschrieben werden kann. Sind die Permissions falsch/zu offen
      # beschwert sich `ssh`.
      chmod 600 $HOME/.ssh/deploy_rsa
      
    - # -h menschenlesbare Formatierung von Zahlen
      # -a Archivmodus, Kürzel für eine Reihe von Flags, die das Erstellen von
      #    Backups bzw. Kopien gesamter Verzeichnisstrukture ermöglichen
      # -c verwendet Checksummen anstelle von Größe und Datum, um zu prüfen, ob
      #    eine Datei übertragen werden muss. Sinnvoll, da z. B. das Änderungsdatum
      #    auf dem CI-Server immer ein anderes sein wird.
      # -z komprimiert Dateien während der Übertragung
      # -e Remote-Shell, die für die Verbindung verwendet werden soll
      #    (standardmäßig `ssh`, `-i` spezifiert den zu verwendenden Key)
      # --delete  Standardmäßig löscht `rsync` keine Dateien im Ziel
      # --exclude Schließt bestimmte Verzeichnisse aus, die nicht deployt
      #           werden sollen.
      
      rsync
        -hacz --delete --info=name1,stats2
        -e "ssh -i $HOME/.ssh/deploy_rsa"
        --exclude=public/router.php --exclude=.DS_STORE --exclude=.git/
        ./{public,site,storage,vendor}
        $TARGET_USER@$TARGET_HOST:/var/www/virtual/mtvdev/api
```
