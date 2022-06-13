# mitmproxy

[mitmproxy](https://mitmproxy.org) ist ein HTTP(S)-Proxy und kann HTTP-Requests und -Responses abfangen und modifizieren. Das ist beispielsweise hilfreich, um zu testen, wie sich Clients bei inkonsistenten API-Responses verhalten.

## Installation

mitmproxy kann über Homebrew installiert werden:

```
brew install mitmproxy
```

Anschließend stehen zwei Commands zur Verfügung, `mitmproxy` und `mitmweb`. Ersterer ist ein CLI, letzeter startet ein webbasiertes GUI. `mitmweb` befindet sich noch in der Entwicklung und unterstützt noch nicht alle Features von `mitmproxy`.

Der Proxy kann mit `mitmproxy -p {PORT}` bzw. `mitmweb -p {PORT}` gestartet werden. Anschließend kann der Proxy in den Netzwerkeinstellungen des Betriebssystems oder Browsers eingetragen werden.

## Requests/Responses abfangen und modifizieren

In der Regel sollen nicht alle Requests/Responses abgefangen werden, sondern nur ausgewählte, beispieslweise alle Requests/Responses an/von einem bestimmten API-Endpunkt. Hierzu können Filterausdrücke angegeben werden. Die Dokumentation enthält [eine vollständige Referenz der verfügbaren Filter](https://docs.mitmproxy.org/stable/concepts-filters/).

Der folgende Filter fängt beispielsweise alle Requests/Responses ab, deren URL `https://example.org/api` matcht:

```
~u https://example.org/api
```

Dieser Request fängt nur Responses ab, deren URL `https://example.org/api` enthält:

```
~u https://example.org/api
```

Sobald mitmproxy einen Request oder eine Response abgefangen hat, wird dieser pausiert, kann bearbeitet und anschließend fortgesetzt werden.
