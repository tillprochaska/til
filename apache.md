# Apache

## `FollowSymLinks` vs. `FollowSymLinksIfOwnerMatch`
Die Option `FollowSymLinksIfOwnerMatch` tut im Prinzip genau das selbe wie `FollowSymLinks`. Apache prüft dann aber zusätzlich, ob der Owner des Symlinks der selbe Nutzer ist, wie der des Ziels. Ein prominentes Beispiel, warum das wichtig ist, [hat Uberspace geliefert](https://blog.jonaspasche.com/2014/07/11/followsymlinks-vs-symlinksifownermatch/).
