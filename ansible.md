# Ansible

## Praktische Rollen
* #### [dev-sec.os-hardening](https://github.com/dev-sec/ansible-os-hardening)
  Setzt sichere Default-Werte für die Systemkonfiguration, z. B. kein Root-Login via SSH, Installation von signierten Packages, …
  
* #### [dev-sec.ssh-hardening](https://github.com/dev-sec/ansible-ssh-hardening)
  Setzt sichere Default-Werte für `sshd`, z. B. Verbot von Passwort-Authentifikation, Beschränkung der Login-Versuche, …

## Privilege Escalation
Der `become`-Parameter kann sowohl für einzelne Tasks als auch für ganze Rollen und Playbooks gesetzt werden. Die entsprechenden Tasks werden dann mit Root-Rechten ausgeführt.

## Debugging von Variablen/Facts
Alle Variablen und Facts für einen bestimmten Host können wie folgt angezeigt werden:

```
ansible -i {{ HOSTFILE }} -m setup {{ HOSTNAME }}
```

## Aliase für Hosts
Ansible ermöglicht es ganz einfach, Hosts einen sprechenderen Namen zu geben:

```ini
[webservers]
web01 ansible_host=1.2.3.4
```

## Variablen via Kommandozeile setzen

Mithilfe der `--extra-vars` bzw. `-e` Flag können Variablen direkt über die Kommandozeile gesetzt werden:

```
ansible-playbook --extra-vars="enable_variant=true log_path=/var/log/ playbook.yml"
```

## Playbook-Ausführung auf Hosts/Gruppen beschränken oder davon ausnehmen

Mithilfe der `--limit` bzw. `-l` Flag kann die Ausführung eines Playbooks auf bestimmte Gruppen oder Hosts beschränkt werden (Whitelist). Es sind auch AND- und OR-Kombination möglich.

```
ansible-playbook --limit 'staging' playbook.yml # Gruppe
ansible-playbook --limit 'webserver_01 # Host
ansible-playbook --limit 'webserver:database' # Hosts, die in Gruppe `webserver` ODER `database`
ansible-playbook --limit `webserver:&staging' # Hosts, die in Gruppen `webserver` UND `staging`
```

Alternativ können auch bestimmte Gruppen oder Hosts ausgenommen werden (Blacklist). Dazu wird vor die entsprechenden Identifier ein `!` gesetzt.

```
ansible-playbook --limit '!production' playbook.yml
ansible-playbook --limit '!webserver_01' playbook.yml
```

## Praktische Tasks

### Mehrere Nutzer hinzufügen

```yml
name: add users
user:
  name: "{{ item }}"
  groups: "{{ admin_group }}"
with_items: "{{ admin_users }}"
become: yes
```

### SSH-Keys hinzufügen

```yml
name: add public ssh keys for all users
authorized_key:
  user: "{{ item }}"
  key: "{{ lookup('file', 'files/' + item + '.key.pub') }}"
with_items: "{{ admin_users }}"
become: yes
```

### Sudorechte für Gruppe

```yml
name: allow password-less sudo for admin group
  lineinfile:
    path: /etc/sudoers
    state: present
    regexp: "^%{{ admin_group }} ALL="
    line: "%{{ admin_group }} ALL=(ALL) NOPASSWD: ALL"
    validate: "/usr/sbin/visudo -cf %s"
  become: yes
```

Der `validate`-Schritt stellt sicher, dass es sich um eine valide Sudoers-Konfiguration handelt, um zu verhindern, dass der Zugriff auf die Maschine unmöglich wird.

## Blöcke
Mithilfe von Blöcken können mehrere Tasks gruppiert werden. So können einfach bestimmte Optionen auf alle Tasks des Blocks angewandt werden. Im folgenden Beispiel werden beide Tasks mit Root-Rechten ausgeführt.

`tasks/main.yml`
```yml
- block:
  
  - yum: fail2ban
    state: latest
    
  - systemd: fail2ban
    state: started
    
  become: yes
```
