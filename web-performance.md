# Web Performance

## Passive Event-Listener
Dass aufwändige Berechnungen in Handlern für Scroll-, Mausrad- oder Touch-Events die Performance einer Webseite stark beeinträchtigen können, ist nichts Neues. Dieses Problem kann leicht umgangen werden, indem die Anzahl der Berechnungen beispielsweise mithilfe von `requestAnimationFrame()` oder `setTimeout()` gedrosselt wird. Darüber hinaus führen moderne Browser Handler für solche Events in separaten Threads aus, um sicherzustellen, dass rechenintensive Berechnungen das eigentliche Scrollen nicht beeinträchtigen.

Es gibt aber einen Haken: Touch- oder Mausrad-Events (`wheel`, `touchstart`, `touchmove`, `touchend`) können programmatisch via `preventDefault()` abgebrochen werden. In diesem Fall muss der Browser warten, bis klar ist, ob das Event durch den Event-Handler abgebrochen wurde – also im Zweifel solange, bis die Ausführung des Event-Handlers abgeschlossen ist.

*Passive Event-Listener* schließen die Möglichkeit, dass ein solches Event mit `preventDefault()` abgebrochen wird, von vorne herein aus, indem dies beim Registrieren des Event-Handlers dem Browser explizit mitgeteilt wird. Bei einem als passiv markierten Event-Listener muss der Browser also nicht die Ausführung des Handlers abwarten.

```js
window.addEventListener('touchstart', handler, { passive: true });
```

⚠️ Browser, die passive Event-Listener nicht unterstützen, erwarten als dritten Parameter einen Boolean-Wert. Dies kann ggf. zu Problemen führen, weshalb Browser-Unterstützung für passive Event-Listener vorher geprüft werden sollte.

⚠️ Da Scroll-Events anders als Mausrad- und Touch-Events nicht abgebrochen werden können, also standardmäßig passiv sind, müssen diese nicht explizit als passiv markiert werden.
