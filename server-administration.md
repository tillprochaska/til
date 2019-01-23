# Server Administration
Notizen zum Aufsetzen und Betreiben eines Webservers (speziell für AWS EC2-Instanzen)

## Inhalt

* [Apache und PHP installieren](#apache-und-php-installieren)
* [SSL-Zertifikat von Let’s Encrypt](#ssl-zertifikat-von-lets-encrypt)
* [Neuen Nutzer erstellen](#neuen-nutzer-erstellen)
* [Logs](#logs)
* [fail2ban](#fail2ban)
* [CDN](#cdn)
* [Logfiles analysieren](#logfiles-analysieren)
* [Server-/X-Powered-By-Header entfernen](#server-x-powered-by-header-entfernen)

## Apache und PHP installieren
Dank Package-Manager ist Apache inkl. PHP unter Amazon Linux mit ein paar Befehlen installiert: Bei einer frischen AMI-Instanz am besten zuerst alle installierten Pakete aktualisieren:

```
$ sudo yum update -y
```

Anschließend httpd24 (Apache 2.4) und die aktuellste PHP-Version (aktuell PHP 7.1) sowie einiger häufig gebrauchter PHP-Erweiterungen installieren.

```
$ sudo yum install -y httpd24 mod24_ssl php71 php71-mbstring php71-gd
```

Apache starten, so konfigurieren, dass es bei jedem Boot automatisch startet und anschließend kurz überprüfen, ob die Einstellung auch übernommen wurde.

```
$ sudo service httpd start
$ sudo chkconfig httpd on
$ chkconfig --list httpd
```

Der letzte Befehl sollte die folgende Ausgabe ergeben:

```
httpd           0:off   1:off   2:on    3:on    4:on    5:on    6:off
```

Den aktuellen Nutzer (bei AMI meist ec2-user) zu einer neuen Gruppe `apache` hinzufügen. Dies macht die Rechteverwaltung, z. B. wenn später weitere Nutzer hinzugefügt werden sollen, leichter. Anschließend Rechte für das Apache-Root-Verzeichnis `/var/www` entsprechend setzen.

```
$ sudo usermod -a -G apache ec2-user
$ sudo chown -R ec2-user:apache /var/www
$ sudo chmod 2775 /var/www
$ find /var/www -type d -exec sudo chmod 2775 {} \;
$ find /var/www -type f -exec sudo chmod 0664 {} \;
```

Sofern das Modul `mod_rewrite` unter Apache genutzt werden soll, muss außerdem in der Apache-Konfiguration unter `/etc/httpd/conf/httpd.conf` für das Verzeichnis `/var/www/html` die Option AllowOverride aktiviert werden, damit in `.htaccess`-Dateien gesetzte Einstellungen Anwendung finden.

```apache
<Directory "/var/www/html">
    ...
    AllowOverride None
    ...
</Directory>
```

Anschlißend Apache neu starten:

```
$ sudo service httpd restart
```

## SSL-Zertifikat von Let’s Encrypt
Amazon Linux AMI wird von `certbot-auto` nicht offiziell unterstützt. Mit den folgenden Schritten war es unter Amazon Linux AMI (Version 2) im April 2018 möglich, `certbot-auto` zu installieren und mit Apache zu verwenden. Die [Anleitung zur Installation von Amazon selbst](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-an-instance.html#letsencrypt) führt zu fehlern bei der Auflösung der notwendigen Packages.

### Zertifikate erstellen
Das Setup-Skript für `certbot-auto` herunterladen:

```
$ cd /usr/local/bin
$ wget https://dl.eff.org/certbot-auto
$ chmod a+x certbot-auto
```

Jetzt kann `certbot-auto` normal und inklusive aller Plugins verwendet werden, z. B. mit dem Webroot-Plugin. Dieses Plugin wird im angegebenen Webroot automatisch Dateien zur Durchführung einer ACME-Challenge erstellen, sodass Zertifikatsabruf und -erneuerung vollautomatisch ohne zusätzliche Nutzerinteraktion stattfinden können.

```
$ /usr/local/bin/certbot-auto certonly --webroot -w /var/www/html -d domain.com -d www.domain.com 
```

Sofern die Domain noch nicht auf den entsprechenden Server aufgeschaltet ist, müssen die Zertifikate mittels manueller ACME-Challenge generiert werden.

```
$ /usr/local/bin/cerbot-auto certonly --manual
```

### Zertifikate automatisch erneuern
Die Zertifikate von Let’s Encrypt sind [aus Gründen](https://letsencrypt.org/2015/11/09/why-90-days.html) nur für 90 Tage gültig. Deshalb sollte der Erneuerungsprozess möglichst automatisiert werden. Mithilfe eines Cronjobs kann `certbot-auto` regelmäßig so ausgeführt werden, dass das Zertifikate bei Bedarf erneuert wird.

Mit `sudo crontab -e` folgendem Eintrag zur Cronjob-Tabelle hinzufügen:

```
MAILTO=webmaster@domain.com
MAILFROM=cron@domain.com
30 3 * * * /usr/local/bin/certbot-auto certonly --no-self-upgrade --quiet --post-hook "/sbin/service httpd graceful" --webroot -w /var/www/html -d domain.com -d www.domain.com
```

Jeden Tag um 3:30 Uhr wird mit dem obigen Aufruf von `certbot-auto` das Zertifikate für die Domains `domain.com` und `www.domain.com` geprüft und ggf. aktualisiert und anschließend der Webserver neugestartet, sodass die neuen Zertifikate verwendet werden. Außerdem wird, sofern der Befehl im Fehlerfall eine Ausgabe erzeugt hat, eine E-Mail an `webmaster@domain.com` gesendet.

#### Hinweis bei Zugriffsbeschränkung
Sollte eine Seite zugriffsbeschränkt sein, muss für die für die ACME-Challenge relevanten URLs eine Ausnahme hinzugefügt werden. Ansonsten schlägt die automatische Erneuerung der Zertifikate fehl.

Ist beispielsweise HTTP Baisc Auth (in einer `.htaccess` o. ä.) wie folgt konfiguriert…

`/var/www/html/.htaccess`
```apache
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/httpd/.htpasswd
Require valid-user
```

… kann eine Ausnahmen hinzugefügt werden, indem im entsprechenden Unterordern, also in diesem Fall `/.well-known/` eine `.htaccess` mit folgendem Inhalt angelegt wird:

`/var/www/html/.well-known/.htaccess`
```apache
Satisfy any
Allow from all
```

### Domains zu bestehendem Zertifikat hinzufügen
Um eine Domain zu einem bestehenden Zertifikat hinzuzufügen, kann `certbot-auto` mit `--expand` aufgerufen werden. Es wird dann ein neues Zertifikat, das für alle aufgeführten Domains gültig ist, erstellt. Um die ACME-Challenges für die Domains durchzuführen, müssen die entsprechenden Plugin-Optionen (z. B. für das Webroot-Plugin) genau wie bei der Zertifikatserstellung oder -erneuerung angegeben werden.

```
/usr/local/bin cerbot-auto certonly --expand --webroot -w /var/www/html -d domain.com -d www.domain.com -d new.domain.com
```

### Apache-Sicherheit erhöhen

Nachdem die Zertifikate erfolgreich erstellt wurden, müssen die entsprechenden Optionen in der Apache-Konfiguration (in der Regel zu finden unter: `/etc/httpd/conf.d/ssl.conf`) angepasst werden. Entweder innerhalb einer VirtualHost-Direktive oder als globale Einstellung.

```apache
SSLCertificateFile /etc/letsencrypt/live/meine-domain.com/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/meine-domain.com/privkey.pem
SSLCertificateChainFile /etc/letsencrypt/live/meine-domain.com/fullchain.pem
```

Standardmäßig unterstützt Apache eine ganze Reihe an Cipher Suiten sowie ältere Protokolle, die zur Verschlüsselung der Kommunikation genutzt werden, von denen einige heute jedoch nicht mehr als sicher gelten. Deshalb sollte die Apache-SSL-Konfiguration wie folgt angepasst werden. Sofern `certbot-auto` mit dem Apache-Plugin verwendet wird, geschiet dies in der Regel automatisch, ansonsten sollten die folgenden Änderungen vorgenommen werden.

Zum Testen des Sicherheitsstandards stellt Qualys ein [umfangreiches Tool](https://www.ssllabs.com) zur verfügung

Die Standard Cipher-Suite auskommentieren (sofern sie das noch nicht ist) und stattdessen folgende Konfiguration einfügen. Gegebenenfalls vorher aktuelle Cipher-Listen recherchieren.

```apache
SSLCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
SSLProxyCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
```

Außerdem in den Zeilen zuvor die folgenden Optionen auskommentieren oder entsprechend ersetzen:

```apache
#SSLHonorCipherOrder on
SSLProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
SSLProxyProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
```

### Probleme mit `certbot-auto`
`certbot-auto` unterstützt offiziell die Amazon Linux Distribution nicht. Außerdem prüft das Programm bei jeder Ausführung, ob ein Update verfügbar ist und installiert dieses gegebenenfalls. Das führt unter Umständen zu Fehlern, die häufig auf fehlende Python-Pakete zurückzuführen sind.

`certbot-auto` wird in einer virtuellen Umgebung und nicht wie in der Regel über einen Package-Manager installiert. Die fehlenden Pakete können in dieser virtuellen Umgebung mit der dort vorhandenne `pip`-Version installiert werden, was die Fehler beheben sollte. Der Pfad zur virtuellen Umgebung kann unter Umständen abweichen, sollte aber aus der Fehlermeldung hervorgehen.

```
sudo /opt/eff.org/certbot/venv/bin/pip install paketname
```

## Neuen Nutzer erstellen
Um einen neuen Nutzer unter Unix (insbesondere AMI) anzulegen und einen SSH-Key zu hinterlegen, folgende Befehle ausführen:

Nutzer anlegen:

```
$ sudo adduser new_user_name
```

Optional Nutzer einer bestehenden Gruppe hinzufügen:

```
$ sudo usermod -a -G group_name new_user_name
```

Zum neu erstellten Nutzer wechseln:

```
$ sudo su - new_user_name
```

Verzeichnis und Datei `authorized_keys` anlegen. In dieser Datei werden später die Public-Keys, mit denen sich eingeloggt werden kann, gespeichert.

```
$ mkdir ~/.ssh
$ touch ~/.ssh/authorized_keys
```

Datei und Verzeichnis mit entsprechenden Rechten versehen. Sollten die Rechte nicht entsprechend strikt gesetzt werden, blockiert sshd grundsätzlich jede Verbindung.

```
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys
```

Zum Schluss den gewünschten SSH-Key hinterlegen:

```
$ echo -e 'ssh-rsa ... \n' >> ~/.ssh/authorized_keys
```

## Logs
* Logs über Anmeldeversuche u. ä.: `/var/log/secure`
* Apache-Logs: `/var/log/httpd/*`

## fail2ban
[`fail2ban`](https://www.fail2ban.org/wiki/index.php/Main_Page) scannt Logdateien wie z. B. die o. g. Apache- und SSH-Logs und erkennt auffällige Muster wie z. B. wiederholte, fehlgeschlagene Anmeldeveresuche innerhalb kurzer Zeit. Dies kann helfen, Brute-Force-Attacken u. ä. Angriffen vorzubeugen.

### Installation
`fail2ban` kann unter Amazon Linux einfach über den Paketmanager installiert werden:

```
$ sudo yum install -y fail2ban
```

`fail2ban` enthält von Haus aus eine relativ umfangreiche Standardkonfiguration, gespeichert im Verzeichnis `/etc/fail2ban/` in `fail2ban.conf` und `jail.conf`, die für die im Regelfall vollkommen ausreichend sein dürfte. Die Standardkonfiguration kann in lokalen Konfigurationsdateien (`fail2ban.local` bzw. `jail.local`) überschrieben werden.

```
$ cd /etc/fail2ban/
$ sudo touch fail2ban.local
$ sudo touch jail.local
```

Anschließend `jail.local` in einem auf dem System verfügbaren Editor (z. B. `nano`) öffnen, die folgende Konfiguration einfügen und ggf. anpassen:

```
# The DEFAULT allows a global definition of the options. They can be overridden
# in each jail afterwards.

[DEFAULT]

# `ignoreip` can be an IP address, a CIDR mask or a DNS host.
# Fail2ban will not ban a host which matches an address in this
# list. Several addresses can be defined using space separator.
ignoreip = 127.0.0.1/8

# `bantime` is the number of seconds that a host is banned.
bantime  = 600

# A host is banned if it has generated `maxretry` during the last
# `findtime` seconds.
findtime  = 600

# `maxretry` is the number of failures before a host get banned.
maxretry = 3

# "backend" specifies the backend used to get files modification.
# Available options are "pyinotify", "gamin", "polling" and "auto".
# This option can be overridden in each jail as well.
maxretry = 3

# "backend" specifies the backend used to get files modification.
# Available options are "pyinotify", "gamin", "polling" and "auto".
# `auto` automatically selects the backend to use depending on
# their availability on the system.
backend = auto

# With `usedns` set to `warn` fail2ban will try to resolve
# domain hosts via the dns.
usedns = warn

# This jail montiors ssh login attempts and blocks hosts
# for 10 minutes (`blocktime`), that fail to login 5 times
# (jail `maxretry`) within 10 minutes (default `findtime`), and
# sends an email to website-alerts@smartpatient.eu containing
# whois information about the host.

[ssh-iptables]

enabled  = true
filter   = sshd
action   = iptables[name=SSH, port=ssh, protocol=tcp]
           sendmail-whois[name=SSH, dest=mymailaddress@example.com, sender=fail2ban@lab.mytherapyapp.com]
logpath  = /var/log/secure
maxretry = 5
```

`fail2ban` starten, so konfigurieren, dass es bei jedem Boot automatisch startet und anschließend kurz überprüfen, ob die Einstellung auch übernommen wurde.

```
$ sudo service fail2ban start
$ sudo chkconfig fail2ban on
$ chkconfig --list fail2ban
```

### Informationen über gesperrte Hosts/IP-Adressen
`fail2ban` stellt mit `fail2ban-client` ein Programm zur Verfügung, mit dem nicht nur die Konfiguration komfortabel angepasst werden kann, sondern auch der Status einzelnen Jails ausgegeben werden kann. Folgender Befehl zeigt den Status des zuvor eingerichteten `ssh-iptables` Jails inklusive der Anzahl der gesperrten Hosts an:

```
sudo fail2ban-client status ssh-iptables
```

## CDN

### CDN konfigurieren
Es gibt grundsätzlich zwei Möglichkeiten, ein CDN für eine Webseite aufzusetzen. Die einfachste Möglichkeit ist es, lediglich statische Assets wie Grafiken, Stylesheets und Javascript-Code über das CDN auszuliefern. Dazu wird die ursprüngliche Domain als Quelle für das CDN konfiguriert und eine Subdomain, z. B. `cdn.mydomain.com`, auf das CDN aufgeschaltet. Im HTML-Dokument müssen nur noch alle URLs angepasst werden, um `cdn.mydomain.com` zu verwenden.

Alternativ können alle Anfragen der Domain über das CDN geleitet werden. Dies funktioniert logischerweise nur bei Inhalten gut, die sich relativ selten ändern. In diesem Fall wird die Hauptdomain, z. B. `www.mydomain.com`, auf das CDN aufgeschaltet. Bei vielen CDN-Anbietern können Domains lediglich per CNAME-Eintrag im DNS aufgeschaltet werden, sodass es technisch praktisch nicht möglich ist, die Wurzeldomain `mydomain.com` auf das CDN aufzuschalten. Stattdessen muss diese auf eine Subdomain wie `www.mydomain.com` weitergeleitet werden.

### Cache Header
Die meisten CDNs nutzen den `Cache-Control`- und/oder `Expires`-Header, um zu prüfen, ob eine Ressource dem Cache hinzugefügt werden soll. Der `Cache-Control`-Header gibt ein relatives Datum für die gültigkeit der gecachten Ressource ab dem Abrufdatum an. Im `Expires`-Header wird hingegen ein absolutes Datum angegeben.

Sofern PHP zum Rendern von HTML-Dokumenten verwendet wird und diese ebenfalls vom CDN ausgeliefert werden sollen, kann es unter Umständen vorkommen, dass trotz entsprechenden Konfiguration des Apache-Webservers die richtigen Cache-Header nicht gesetzt werden. Dies wird wahrscheinlich dadurch verursacht, dass in PHP eine Session gestartet wird, was wiederum bei Standardkonfiguration dazu führt, dass die folgenden Header gesetzt werden:

```
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: private, max-age=(session.cache_expire in der Zukunft), pre-check=(session.cache_expire in der Zukunft)
Last-Modified: (der Zeitstempel der letzten Session-Speicherung)
```

Dies kann unterbunden werden, indem die Konfigurationsoption `session.cache_limiter` explizit auf `public` gesetzt wird:

```php
session_cache_limiter('public');
session_cache_expire(180); // Gültigkeitsdauer des Caches in Minuten, Standard: 180
```

## Logfiles analysieren
[GoAccess](https://goaccess.io/) ist ein praktisches Tool, das Logfiles in verschiedenen Formaten, z. B. von Apache Webservern oder Amazons CloudFront CDN analysieren und hübsch aufbereiten kann. Nachdem GoAccess z. B. mithilfe von Homebrew installiert wurde, kann beispielsweise mit folgendem Befehle eine Auswertung eines Logfiles im *Combined Logfile Format* (CLF) `access.log` als HTML-Datei `access_report.html` erstellt werden:

```
goaccess access.log -o access_report.html --log-format=COMBINED
```

## Server-/X-Powered-By-Header entfernen
Standardmäßig exposen Apache und PHP über den `Server` sowie den `X-Powered-By`-Header detaillierte Informationen über die Apache-, PHP- und Betriebssystem-Version. Diese Informationen werden teilweise auch auf automatisch von Apache generierten Fehlerseiten angezeigt. Durch Anpassen der `httpd.conf` kann dies deaktiviert werden:

```apache
# Remove detailed server, PHP and OS information from response
# headers and error pages
ServerTokens Prod
ServerSignature Off
Header always unset "X-Powered-By"
Header unset "X-Powered-By"
```

Danach muss Apache nur noch neugestartet werden – schon wird im Server-Header nur noch `Apache` angezeigt, nicht aber die genaue Version:

```
sudo service httpd graceful
```
