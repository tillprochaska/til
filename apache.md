# Apache

## `FollowSymLinks` vs. `FollowSymLinksIfOwnerMatch`
Die Option `FollowSymLinksIfOwnerMatch` tut im Prinzip genau das selbe wie `FollowSymLinks`. Apache prüft dann aber zusätzlich, ob der Owner des Symlinks der selbe Nutzer ist, wie der des Ziels. Ein prominentes Beispiel, warum das wichtig ist, [hat Uberspace geliefert](https://blog.jonaspasche.com/2014/07/11/followsymlinks-vs-symlinksifownermatch/).

## Anwendung weiterer RewriteRules verhindern
Mittels der `L`-Flag (bzw. `last`) kann bei Verwendung von `mod_rewrite` verhindert werden, dass weitere, nachfolgende Rewrite Rules angewendet werden. Das funktioniert jedoch nicht immer: Wird die Anfrage auf eine URL umgeschrieben, in deren Kontext die Rewrite Rules ebenfalls gelten, werden sie erneut angewendet.

Befinden sich z. B. statische Dateien in einem Unterverzeichnis, der Verzeichnis-Präfix soll aber nicht sichtbar sein, so scheinen auf den ersten Blick die folgenden Regeln erfolgversprechend:

```
.
├── 404.html
└── static
    └── favicon.ico
```

```apache
RewriteCond %{DOCUMENT_ROOT}/static/$1
RewriteRule (.*) static/$1 [L]

RewriteRule static/(.*) 404.html [L]
```

Dabei wird der Aufruf von `/favicon.ico` tatsächlich nach `/static/favicoin.ico` aufgelöst. Die Regeln werden dann jedoch erneut angewandt und die zweite Regel greift, sodass der Aufruf final nach `404.html` aufgelöst wird.

Dies kann auf zwei Arten verhindert werden: Die [`END`-Flag](http://httpd.apache.org/docs/current/rewrite/flags.html#flag_end) funktioniert genau wie die `L`-Flag, verhindert aber die erneute Anwendung der Regel in Folge von internen Redirects. Die `END`-Flag wird jedoch erst in neueren Apache-Versionen verfügbar.

```apache
RewriteCond %{DOCUMENT_ROOT}/static/$1
RewriteRule (.*) static/$1 [END]
RewriteRule static/(.*) 404.html [L]
```

Alternativ kann eine Rewrite Condition verwendet werden, die auf die Umgebungsvariable `REDIRECT_STATUS` zugreift. Diese Variable ist initial leer (matcht also auf `^$`), wird jedoch bei einem erfolgreichen (internen) Redirect mit dem entsprechenden Status-Code gefüllt.

```apache
RewriteCond %{DOCUMENT_ROOT}/static/$1
RewriteRule (.*) static/$1 [END]

RewriteCond %{ENV:REDIRECT_STATUS} ^$
RewriteRule static/(.*) 404.html [L]
```
