# Security

## Port Scanning mit `nmap`

`nmap` unterstützt die folgenden Methoden, um offene Ports zu finden:

* TCP Connect Scan
* TCP SYN Scan
* TCP ACK Scan
* TCP NULL Scan
* TCP FIN Scan
* TCP Xmas Scan

## Eigenschaften kryptografischer Hashfunktionen

* **Pre-image resistance**: Zu einem gegebenen Hash `h` ist es nur schwer möglich, eine Nachricht `m` zu generieren, sodass `h = hash(m)`
* **Second pre-image resistance**: Zu einer gegebenen Nachricht `m` ist es nur schwer möglich, eine weitere Nachricht `m'` zu generieren, sodass `hash(m) = hash(m')`
* **Collision resistance**: Es ist nur schwer möglich, zwei Nachrichten `m` und `m'` zu finden, sodass `hash(m) = hash(m')`.

## Salt & Pepper

* **Salt**: String, der für jeden Nutzer eindeutig ist und in der Datenbank gespeichert wird.
* **Pepper**: String, der für alle Nutzer gleich ist und nicht in der Datenbank gespeichert wird.

## Tools

* **John the Ripper**: Programm zum Testen/Finden von Passwörtern mithilfe verschiedener Methoden wie z. B. 
* **hashcat**

## Diffie-Hellman-Merkle

* Nutz Einwegfunktionen , die leicht zu berechnen sind, deren Umkehrfunktionen aber sehr schwer zu berechnen sind
* Es existiert dennoch die Gefahr einer Man-in-the-Middle-Attacke, bei der ein Angreifer den Verbindungsaufbau manipuliert. 
