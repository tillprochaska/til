# HTML

## `accesskey`-Attribut

Browser unterstützen das `accesskey`-Attribut, um Keyboard-Shortcuts für bestimmte Elemente zu definieren. Im folgenden Beispiel kann das Suchfeld beispielsweise unter Safari mit dem Shortcut `alt + ctrl + s` fokussiert werden. Die Key-Sequenz für den Accesskey unterscheidet sich jedoch je nach Plattform/Browser.

```html
<input type="search" accesskey="s" />
```

## Untertitel

Untertitel, die keinen eigenen Abschnitt einleiten, dürfen nicht in Tags für Überschriften (`h1` bis `h6`) umschlossen werden. Aus dem Standard:

> For example, the following snippet, intended to represent the heading of a corporate site, is non-conforming because the second line is not intended to be a heading of a subsection, but merely a subheading or subtitle (a subordinate heading for the same section).
> 
> ```html
> <body>
> <h1>ACME Corporation</h1>
> <h2>The leaders in arbitrary fast delivery since 1920</h2>
> ...
> ```
  
  Zwar gibt es theoretisch das `hgroup`-Element, allerdings hat dies effektiv keinen relevanten Einfluss. Die beste Lösung ist deshalb, schlicht ein `p`-Tag für Untertitel etc. zu verwenden.
  
  ```html
  <h1>ACME Corporation</h1>
  <p class="subtitle">The leaders in arbitrary fast delivery since 1920</p>
  ```
  
  * [Standard](https://html.spec.whatwg.org/#semantics-2)
  * [Adrian Roselli](https://twitter.com/aardrian/status/1318583839934078979)
