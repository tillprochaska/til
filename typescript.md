# TypeScript Notes

Notizen zu TypeScript und zur Konfiguration des Compilers und weiterem Tooling

## `tsconfig.json`
Die `tsconfig.json` Datei spezifiziert zum einen Einstiegsdateien (sollte TypeScript nicht aus einem Webpack Loader o. ä. heraus verwendet werden. Zum anderen enthält sie auch Compiler-Optionen, mit dem der Compiler angepasst werden kann. Wird der TypeScript-Compiler über das Kommandozeilenprogramm aufgerufen, können die Compile- Optionen auch als Flags übergeben werden.

### Optionen
#### `target` (String, Standard: `es3`)
TypeScript kann Code so kompilieren, dass er mit bestimmten ECMAScript-Versionen kompatibel ist. Wird TypeScript in einem Build-Prozess zusammen mit einem Transpiler wie z. B. *Babel* oder *Bublé* verwendet, reicht es aus, als Kompilierungs-Ziel eine der letzten ES-Versionen (z. B. `es2017`) zu verwenden, schließlich wird der kompilierte TypeScript-Code zum Schluss, ggf. zusammen mit weiterem JavaScript-Quelltext, erneut transpiliert.

#### `strictNullChecks` (Boolean, Standard: `false`)
In der Standardkonfiguration sind `null` und `undefined` valide Werte für jeden Typ. Wird `strictNullChecks` auf `true` gesetzt, können `null` bzw. `undefined` nur zugewiesen werden, wenn die Typdeklaration dies explizit vorsieht.

## `awesome-typescript-loader`
`awesome-typescript-loader` ist neben dem offizielen `ts-loader` ein viel verwendeter Webpack-Loader für TypeScript-Dateien.

### Optionen
#### `configFileName` (String, Standard: `tsconfig.json`)
Die `configFileName`-Option gibt den Pfad zur `tsconfig.json`-Konfigurationsdatei an.
