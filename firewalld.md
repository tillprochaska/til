# firewalld

## Services
Services sind letzten Endes Listen vordefinierter Ports/Protokolle, die es einfacher machen, den Zugang zu einem bestimmten Service zu ermöglichen. Beispielsweise laufen HTTP-Server in der Regel unter Port 80.

Anstatt manuell den entsprechenden Port zu öffnen:

```
firewall-cmd --add-port=80/tcp
```

kann mit dem gleichen Effekt einfach der Service hinzugefügt werden:

```
firewall-cmd --add-service=http
```

Neben einer Liste von Ports enthalten Services auch eine kurze Beschreibung. Eine Liste der Standard-Services findet sich im Verzeichnis `/usr/lib/firewalld/services`. Dort ist für jeden Service eine XML-Konfigurationsdatei hinterlegt.

Eigene Services können ebenfalls mithilfe von `firewalld-cmd` angelegt werden:

```
firewall-cmd --permanent --service=myservice --set-description="Short description"
firewall-cmd --permanent --service=myservice --set-short="Short title"
firewall-cmd --permanent --service=myservice --add-port=port/protocol
```
