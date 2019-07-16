# JavaScript

## Objekt ohne Prototype/Vererbung

Mithilfe von `Object.create(null)` wird ein neue Objekt ohne `Object.prototype` erstellt. Im Gegensatz dazu erzeugen Objektliterale (`{}`) oder auch die Instanziierung mit `Object.create({})` ein neues Objekt mit `Object.prototype` als Prototypen.

## `parseInt`

`parseInt(string, base)` akzeptiert zwei Argumente, einen String sowie (optional) die Basis des Zahlensystems, in dem die Zahl in `string` angegeben ist. Wird `base` allerdings weggelassen, wird nicht – wie vernünftigerweise angenommen werden könnte – als sinnvoller Default die Basis `10` gewählt. Stattdessen versucht `parseInt` anhand des Eingabestrings die Basis abzuleiten. In Standardfällen funktioniert das ohne Probleme:

```js
parseInt('15') // 15
parseInt('15EUR') // 15
parseInt('0xF') // 15 (von Hexadezimal)
```

In anderen Fällen wird es aber unübersichtlich. Je nach ECMAScript-Implementierung kann eine führende Null dazu führen, dass die folgenden Zeichen als Oktalziffern interpretiert werden.

```js
parseInt('017') // 17, falls ES-Standard respektiert
parseInt('017') // 15, andernfalls von Dezimal
```

Und enthält der Eingabestring zufällig ein 'x', dann wird der String unter Umständen fälschlicherweise hexadezimal interpretiert. Beispielsweise im Falle von Währungskürzeln wie dem [Ostkaribischen Dollar](https://www.xe.com/currency/xcd-east-caribbean-dollar).

```js
parseInt('0XCD') // 205 (von Hexadezimal)
```

Um auf der sicheren Seite zu sein, deshalb *immer* die Basis angeben – selbst in scheinbar sicheren Fällen.

## `String.prototype.replace`

`replace(pattern, replacement)` akzeptiert neben dem Suchmuster (Substring oder Regex) als zweiten Parameter `replacement` entweder einen Ersatzstring oder eine Funktion, die einen solchen zurückgibt. Mit letzterer ist es möglich, komplexe, dynamische Ersetzungen vorzunehmen. Doch auch ein einfacher Ersatzstring [erlaubt dynamische Ersetzungen](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace#Specifying_a_string_as_a_parameter), z. B. um auf den gematchten String, oder den Text in seiner Umgebung zuzugreifen:

```js
"Hallo Welt".replace("Hallo", "$'") // " Welt Welt" (Ersetzung mit nachfolgendem String)
```

Das kann insbesondere in Verbindung mit Regexes als Suchmuster sehr praktisch sein, kann aber auch [überraschend kommen]((https://www.peterkroener.de/string-prototype-replace-ist-eine-falle/). Im Falle von unvorhersehbaren Replacement-Stringse also am besten eine Replace-Funktion verwenden:

```js
string.replace("Hallo", () => replacementString);
```
