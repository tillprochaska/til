# Slate.js

## Blöcke ohne Kinder / nicht editierbare Blöcke

Standardmäßig müssen Slate.js-Blöcke immer alle Kind-Nodes rendern, andernfalls kommt es zu Runtime-Fehlern. Indem für den entsprechenden Block im Schema die Eigenschaft `isVoid` auf `false` gesetzt wird, kann dies verhindert werden. Dann sind Nutzer nicht mehr in der Lage, den Inhalt des Blocks zu ändern.

Alternativ kann auch einfach das `contentEditable`-Attribut des Blocks auf `false` gesetzt werden. Da Slate davon allerdings »nichts mitbekommt«, müssen die Kindern (auch wenn sie leer sind) trotzdem gerendert werden. Insofern ist diese Methode nur vorzuziehen, falls beispielsweise das Schema nicht bearbeitet werden kann.
