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
