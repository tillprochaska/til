# PHP

## PHP und türkische Spracheinstellungen

Während Variablennamen in PHP case-sensitiv sind, sind Funktions-/Methoden- und Klassennamen case-insensitive. Dazu wandelt PHP all diese Namen in Kleinbuchstaben um. Zumindest auf Posix-Systemen sind die hierzu verwendeten Funktionen allerdings locale-aware, es werden also die Sprachregeln der Loakalisation verwendet.

Im Türkischen sind »i« und »I« nicht wie in den meisten anderen Sprachen die kleine bzw. große Variante des gleichen Buchstaben. Vielmehr gibt es hier ein i (mit Punkt) und ein ı (ohne Punkt), die jeweils eine kleine und eine große Variante haben.

Sofern Türkisch die Sprache der Lokalisation ist, so wird z. B. aus dem Klassennamen `Iterator` `ıterator`. Als das Script geparst wurde, war die Sprache jedoch vermutlich noch nicht gesetzt, PHP kennt die Klasse also eigentlich unter `iterator` (dieses Mal mit Punkt), `ıterator` (ohne Punkt) existiert hingegen nicht; es wird ein Fehler geworfen.

* [Bug-Report und Hintergrund](https://bugs.php.net/bug.php?id=18556#1316116767)
