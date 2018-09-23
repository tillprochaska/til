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
