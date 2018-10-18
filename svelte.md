# Svelte

Svelte ist ein JavaScript UI-Framework, dass im Gegensatz zu vielen anderen populären UI-Frameworks zur Build-Zeit in Vanilla-JavaScript kompiliert wird. Da keine Framework-Runtime eingebunden werden muss, sind die Implementierungen insbesondere bei kleinen Anwendungen deutlich kleiner als bei Verwendung alternativer Frameworks. Dank statischer Analyse zur Compilezeit und der Verwendung einfacher DOM-Manipulation ist Svelte außerdem häufig deutlich schneller – und das ganz ohne Virtual DOM.

## Sapper
Sapper ist ein Framework für (Progressive) Web Apps, das auf Svelte basiert.

### Store-Methoden aus Templates aufrufen
Translation- oder API-Helper werden häufig von allen Komponenten benötigt und verwendet. Aus diesem Grund macht es Sinn, diese über einen globalen Store verfügbar zu machen. In den Komponenten können die Methoden dann normal über `this.store.myMethod()` aufgerufen werden. In Templates sind allerdings nur die Attributes des Stores verfügbar. Um trotzdem Store-Methoden (wie z. B. einen Translation-Helper) aus den Templates heraus aufrufen zu können, müssen diese aus den Attribute des Stores referenziert werden. Das ist grundsätzlich kein Problem und kann z. B. durch Erweitern des Svelte-Stores einfach gelöst werden.

```js
// my-store.js

import { Store } from 'svelte/store.js';

class MyStore extends Store {

    constructor(data) {
        super(data);
        
        // Referenziert die `translate(…)` Methode unter
        // dem `t`-Attribut des Stores.
        this.set({
            t: this.translate,
        });
    }

    translate(key) {
        // Übersetzung für `key` zurückgeben
    }

}
```

In Templates kann nun über den gewohnten `$`-Präfix auf Attribute des Stores zugegriffen werden:

```html
// App.html

<p>{ $t('translationKey') }</p>
```

Um den Zustand des Stores vom Server and den Client übergeben zu können, serialisiert Sapper den Store mithilfe von [`devalue`](https://github.com/Rich-Harris/devalue). Damit das Funktioniert, muss der Zustand ein einfaches JavaScript-Objekt – es darf also unter anderem keine Funktionsreferenzen haben. Dies kann umgangen werden, indem die Referenz auf die Helper-Methode wieder gelöscht, sollte der Store-Zustand aus [`sveltejs/sapper/src/middleware.ts`](https://github.com/sveltejs/sapper/blob/master/src/middleware.ts#L430) zum Serialisieren abgerufen werden. Dies ist mit einem kleinen Hack durch Überschreiben der `get()`-Methode möglich:

```js
get() {
    let data = super.get.apply(this, arguments);
    const stackTrace = new Error().stack || '';

    if(stackTrace.indexOf('middleware.js') >= 0) {
        data = Object.assign({}, data)
        delete data.t;
    }

    return data;
}
```

Quelle: [sveltejs/sapper#230](https://github.com/sveltejs/sapper/issues/230#issuecomment-379493101)

## Globale Styles
Häufig verfügen auch komplexe Anwendungen über einen Grundstock an globalen Styles, die normal kaskadieren sollen. Handelt es sich dabei um »plain« CSS ohne Build-Step (z. B. einfache Resets), kann dieser über einen `style`- oder `link`-Tag einfach in die `template.html` eingefügt werden.

Ein wenig komplizierter wird es allerdings, wenn Prä- oder Postprozessoren zum Einsatz kommen sollen. Die einfachste Variante: Die globalen Styles können separat behandelt, das Build-Artefakt kann anschließend auf die gleiche Arte und Weise eingebunden werden.

```html
<style>
    @import 'global.css';
</style>
```

Zusammen mit [`postcss-import`](https://github.com/postcss/postcss-import) könnten so globale Styles eingebunden werden – wären nicht alle Styles in Svelte per Default gescoped. Um das zu umgehen muss jeder Selektor mittel `:global()` davon ausgenommen werden. Mithilfe von [`postcss-import-global`](https://github.com/tillprochaska/postcss-import-global) kann das auch automatisiert werden.


```html
<!-- _layout.html -->
<style>
    @import-global 'global.css';
</style>
```

```css
// global.css
body {
    color: green;
}
```

`postcss-import-global` transformiert `global.css` wie folgt, wodurch es von Svelte/Sapper verschont bleibt.

```css
:global(body) {
    color: green;
}
```

* Es gibt einen RFC ([sveltejs/svelte#901](https://github.com/sveltejs/svelte/issues/901)) zum Vorschlag, `global`-Attribut für `style`-Tags einzuführen, das den gesamten Inhalt des Tags als global markiert und damit vom Scoping ausnimmt.
* Webpack und Rollup erkennen Änderungen in importierten Dateien nicht. Der Build muss nach erfolgten Änderungen manuell neu angestoßen werden. Sobald Svelte das Deklarieren von Abhängigkeiten durch Präprozessoren unterstützt ([sveltejs/svelte#1720](https://github.com/sveltejs/svelte/issues/1720)), könnte sich das ändern.
