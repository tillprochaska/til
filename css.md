# CSS

## `height: 100vh` vs. `height: 100%` on `html`

In some mobile browsers, especially mobile Safari, `100vh` will be the height of the viewport *including* the bottom toolbar, i. e. `100vh` is the viewport’s height after the user has scrolled down and the browser UI has been hidden. This has been introduced in order to prevent reflows and unexpected style updates due to `vh` values changing while the user scrolls down.

Applying `height: 100%` to the `html` element will return the actual height of the visual viewport in those cases.

* [WebKit Bug-Report und Diskussion](https://bugs.webkit.org/show_bug.cgi?id=141832)
* [Verhalten in Chrome unter Android](https://developers.google.com/web/updates/2016/12/url-bar-resizing)
* [Vorschlag `env(viewport-nav-resize)` Umgebungs-Variable](https://github.com/w3c/csswg-drafts/issues/2630)

## `text-decoration` für `::after`/`::before`-Pseudoelemente

Grundsätzlich kann mithilfe der `::after`- und `::before`- Pseudoelemente Text in ein Element eingefügt werden. Dadurch wird der über die `content`-Eigenschaft angegebene Text Teil des Inhalts des Elternelements. In der Folge können auf die Pseudoelemente keine separaten `text-decoration` Styles angewandt werden. `display: inline-block` auf dem Kindelement löst dieses Problem.

Damit diese Lösung auch im IE 11 funktioniert, muss zunächst explizit `text-decoration: underline` definiert werden, nur um es anschließend in einem separaten (!) Ruleset wieder zurückzusetzen:

```css
a::before {
  text-decoration: underline;
}

a::before {
  text-decoration: none;
}
```
 
* [CSS 2.1 Specs](https://www.w3.org/TR/2011/REC-CSS2-20110607/selector.html#before-and-after)
* [StackOverflow](https://stackoverflow.com/questions/1238881/text-decoration-and-the-after-pseudo-element-revisited)

## CSS Grid Autoplacement im Internet Explorer 11

IE 11 unterstützt einige einfache Grid-Features (mit dem Präfix `-ms-`), allerdings leider kein Autoplacement von Grid-Zellen. D.h. die Zellen müssen explizit mithilfe von `-ms-grid-row` und `-ms-grid-column` im Grid positioniert werden, z.B. so:

```css
.grid > *:nth-element(1) {
  -ms-grid-row: 1;
  -ms-grid-column: 1;
}

.grid > *:nth-element(2) {
  -ms-grid-row: 1;
  -ms-grid-column: 2;
}

...
```

Das ist etwas umständlich, allerdings kann Autoprefixer das übernehmen, sofern die Gesamtanzahl an Zellen bekannt und mittels `grid-template-rows` und `grid-template-columns` gesetzt ist.

* [Autoprefixer-Doku zu Grid-Autoplacement](https://github.com/postcss/autoprefixer#grid-autoplacement-support-in-ie)

## `@property` rule

Die `@property`-Rule ermöglicht es, Custom Properties zu *registrieren*, d.h. ihnen nicht nur einen Wert zu geben, sondern auch einen Typ und Vererbungsverhalten zu deklarieren.

```css
@property --color-brand {
  syntax: '<color>';
  inherits: true;
  initial-value: blue;
}
```

Diese `@property`-Regel ist Teil der APIs, die mit CSS Houdini eingeführt werden, entsprechend ist noch kein flächendeckender Support vorhanden. Es handelt sich um eine progressive Erweiterung. Außerdem können bereits heute [Präprozessoren/Linter](https://github.com/stylelint/stylelint/issues/5061) von der Registrierung gebrauch machen.  

* [MDN](
