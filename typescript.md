# TypeScript Notes

Notizen zu TypeScript und zur Konfiguration des Compilers und weiterem Tooling

## `tsconfig.json`
Die `tsconfig.json` Datei spezifiziert zum einen Einstiegsdateien (sollte TypeScript nicht aus einem Webpack Loader o. ä. heraus verwendet werden. Zum anderen enthält sie auch Compiler-Optionen, mit dem der Compiler angepasst werden kann. Wird der TypeScript-Compiler über das Kommandozeilenprogramm aufgerufen, können die Compile- Optionen auch als Flags übergeben werden.

### Optionen
#### `target` (String, Standard: `es3`)
TypeScript kann Code so kompilieren, dass er mit bestimmten ECMAScript-Versionen kompatibel ist. Wird TypeScript in einem Build-Prozess zusammen mit einem Transpiler wie z. B. *Babel* oder *Bublé* verwendet, reicht es aus, als Kompilierungs-Ziel eine der letzten ES-Versionen (z. B. `es2017`) zu verwenden, schließlich wird der kompilierte TypeScript-Code zum Schluss, ggf. zusammen mit weiterem JavaScript-Quelltext, erneut transpiliert.

#### `strictNullChecks` (Boolean, Standard: `false`)
In der Standardkonfiguration sind `null` und `undefined` valide Werte für jeden Typ. Wird `strictNullChecks` auf `true` gesetzt, können `null` bzw. `undefined` nur zugewiesen werden, wenn die Typdeklaration dies explizit vorsieht.

#### `moduleResolution` (String, Standard: `classic` oder `node`)
Hiermit wird festgelegt, wie der TypeScript-Compiler Module aus `import`-Statements auflöst. Die beiden Strategien unterscheiden sich im wesentlichen in der Behandlung von nicht relativen Modulen.

Hierbei sucht `classic` zunächst im Verzeichnis der referenzierenden Datei nach Dateien mit dem gegebenen Modulnamen und der Endung `.ts` bzw. `.d.ts`. Anschließend werden entsprechende Dateien in den übergeordneten Verzeichnissen bis zum TypeScript-Root gesucht. `node` geht im Prinzip genau so vor, berücksichtigt allerdings auch `node_modules`-Verzeichnisse.

Um via `npm` oder `yarn` installierte Packages importieren zu können, muss `modulesResolution` also auf `node` gesetzt werden.


### Quellen & Mehr
* [Compiler Options](https://www.typescriptlang.org/docs/handbook/compiler-options.html)
* [Module Resolution](https://www.typescriptlang.org/docs/handbook/module-resolution.html)

## `awesome-typescript-loader`
`awesome-typescript-loader` ist neben dem offizielen `ts-loader` ein viel verwendeter Webpack-Loader für TypeScript-Dateien.

### Optionen
#### `configFileName` (String, Standard: `tsconfig.json`)
Die `configFileName`-Option gibt den Pfad zur `tsconfig.json`-Konfigurationsdatei an.

## Verschiedenes
### JavaScript-Module ohne Typdeklarationen importieren
Grundsätzlich können auch JavaScript-Module ohne Typdeklarationen importiert werden. Da der Compiler in diesem Fall aber nicht weiß, welchen Typ (und in der Folge welche Attribute und Methoden) das importierte Modul hat, führt dies zu Fehlern, sobald auf Attirbute oder Methoden zugegriffen wird. Dies kann verhindert indem der Typ explizit als `any` deklariert wird. Entweder in einer externen Typdeklaration oder - sofern das Modul nur einmal importiert wird – direkt nach dem Import.

```js
import { Module } from 'module';
const MyModule: any = Store;
```

Auf `MyModule` kann jetzt beliebig zugegriffen werden, ohne das der Compiler Fehler wirft.
