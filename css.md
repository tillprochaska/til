# CSS

## `height: 100vh` vs. `height: 100%` on `html`

In some mobile browsers, especially mobile Safari, `100vh` will be the height of the viewport *including* the bottom toolbar, i. e. `100vh` is the viewport’s height after the user has scrolled down and the browser UI has been hidden. This has been introduced in order to prevent reflows and unexpected style updates due to `vh` values changing while the user scrolls down.

Applying `height: 100%` to the `html` element will return the actual height of the visual viewport in those cases.

* [WebKit Bug-Report und Diskussion](https://bugs.webkit.org/show_bug.cgi?id=141832)
* [Verhalten in Chrome unter Android](https://developers.google.com/web/updates/2016/12/url-bar-resizing)
* [Vorschlag `env(viewport-nav-resize)` Umgebungs-Variable](https://github.com/w3c/csswg-drafts/issues/2630)
